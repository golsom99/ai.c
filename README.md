# ai.c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <time.h>
#include <SDL/SDL.h>
#include <SDL/SDL_image.h>
#include <SDL/SDL_mixer.h>
#include "fonctions.h"



/*________________________________________________________________________________________*/



void initialisation(Acteurs* acteurs)
{
SDL_Surface* tmp  ;	

	tmp = SDL_LoadBMP("stage1.bmp");
	acteurs->backg = SDL_DisplayFormat(tmp);
	SDL_FreeSurface(tmp);
	acteurs->camera.x = 0;
	acteurs->camera.y = 0;
	acteurs->camera.w = SCREEN_WIDTH;
	acteurs->camera.h = SCREEN_HEIGHT;
}



/*________________________________________________________________________________________*/



/*---fonction d initialisation de l hero---*/

void initializeHero(Hero* hero)
{
	SDL_Surface* tmp;

	tmp = SDL_LoadBMP("sprite_hero_right.bmp");
	hero->sprite = SDL_DisplayFormat(tmp);
	SDL_FreeSurface(tmp);
	SDL_SetColorKey(hero->sprite, SDL_SRCCOLORKEY, SDL_MapRGB(hero->sprite->format, 255, 255, 255));


	//Indique l'état et la direction de notre héros
	hero->direction = RIGHT;
	hero->etat = IMMOBILE;

	//Réinitialise le timer de l'animation et la frame
	hero->frame_timer = TIME_BETWEEN_2_FRAMES;
	hero->frame_number = 0;

	/* Coordonnées de démarrage de notre héros */
	hero->x = 0 ;
	hero->y = 120 ;

	hero->Ysaut = 5;

	

}



/*________________________________________________________________________________________*/



/*---fontion pour afficher l'hero ---*//////////////////////////

void drawHero(Hero hero , Acteurs *acteurs)
{

/* Rectangle de destination à blitter */
SDL_Rect dest;
dest.x = hero.x - acteurs->camera.x;
dest.y = hero.y ;

SDL_Rect frame;
frame.x = hero.frame_number*PLAYER_WIDTH;
frame.y = 0;
frame.w = PLAYER_WIDTH;
frame.h = PLAYER_HEIGHT;

/* Blitte notre héros sur l'écran aux coordonnées x et y */
SDL_BlitSurface(acteurs->backg, &acteurs->camera, acteurs->screen, NULL);
SDL_BlitSurface(hero.sprite , &frame , acteurs->screen , &dest) ;
SDL_SetColorKey(hero.sprite, SDL_SRCCOLORKEY, SDL_MapRGB(hero.sprite->format, 255, 255, 255));
}


/*________________________________________________________________________________________*/



/*---fonction de deplacement de l hero---*/

void updatePlayer(Hero* hero , Acteurs* acteurs)
{	
	int resultat_de_collision_droite = 0,
	resultat_de_collision_gauche =0 ,
	resultat_de_collision_bas =0 ;

	hero->PLAYER_SPEED=12 ;




	if(acteurs->event.type == SDL_KEYDOWN)
	{
		if(acteurs->event.key.keysym.sym == SDLK_RIGHT || acteurs->event.key.keysym.sym == SDLK_SPACE)
		{



			if (acteurs->event.key.keysym.sym == SDLK_SPACE )
			{

			hero->PLAYER_SPEED += PLAYER_ACCELERATION ;

			}	


			if(hero->etat == IMMOBILE || hero->etat == WALK_LEFT)
			{
				hero->etat = WALK_RIGHT;
			}
			if(hero->direction == LEFT)
				{
					hero->direction = RIGHT ;
				}
			hero->sprite = SDL_LoadBMP("sprite_hero_right.bmp") ;
			SDL_SetColorKey(hero->sprite, SDL_SRCCOLORKEY, SDL_MapRGB(hero->sprite->format, 255, 255, 255));
			hero->x += hero->PLAYER_SPEED;
			if (hero->x + PLAYER_WIDTH >= Backg_W)
				{
					hero->x = Backg_W - PLAYER_WIDTH ;
				}

			

			
		}


		else if(acteurs->event.key.keysym.sym == SDLK_LEFT )
		{   
			
			if(hero->etat == IMMOBILE || hero->etat == WALK_RIGHT)
			{
				hero->etat = WALK_LEFT;
			}
			if(hero->direction == RIGHT)
				{
					hero->direction = LEFT ;
				}
			hero->sprite = SDL_LoadBMP("sprite_hero_left.bmp") ;
			SDL_SetColorKey(hero->sprite, SDL_SRCCOLORKEY, SDL_MapRGB(hero->sprite->format, 255, 255, 255));
			hero->x -= hero->PLAYER_SPEED ;

			


            if (hero->x < 0)
				{
					hero->x = 0 ;
				}
		}
	} 

    if(acteurs->event.key.keysym.sym == SDLK_UP)
	{
		herojump(hero);
	}  
    if(acteurs->event.type == SDL_KEYUP)
	{
		if(hero->etat != IMMOBILE)
		{
			hero->etat = IMMOBILE;
		}
	}

	
}

void herojump(Hero* hero)
{
	if(hero->jump == NON && hero->ground == OUI)
	{
		hero->jump = OUI;
		hero->ground = NON;
		hero->Ysaut = -28;
		hero->y -= 5;
	}
}


/*________________________________________________________________________________________*/


//fonction pour annimer l'hero

void animationhero(Hero* hero, Acteurs acteurs)
{
	if(acteurs.event.type == SDL_KEYDOWN)
	{
		if(acteurs.event.key.keysym.sym == SDLK_LEFT || acteurs.event.key.keysym.sym == SDLK_RIGHT || acteurs.event.key.keysym.sym == SDLK_SPACE)
		{
			if(hero->frame_timer <= 0)
			{
				hero->frame_number++;
				SDL_Delay(15);
			if(hero->frame_number > MAXF-1)
				hero->frame_number = 0;
			}
		else
		hero->frame_timer--;
		SDL_Delay(15);

		}
	}
	
	if(acteurs.event.type == SDL_KEYUP)
	{
		if(hero->direction == RIGHT)
		{
			hero->frame_number = 0;
		}
		else if(hero->direction == LEFT)
		{
			hero->frame_number = MAXF-1;
		}
	}
}
/*________________________________________________________________________________________*/


/*---fonction d initialisation de l enemy---*/

void initializeEnemy(Enemy* enemy)
{
	SDL_Surface* tmp  ;

	tmp = SDL_LoadBMP("e1.bmp");
	enemy->sprite = SDL_DisplayFormat(tmp);
	SDL_FreeSurface(tmp);
	SDL_SetColorKey(enemy->sprite, SDL_SRCCOLORKEY, SDL_MapRGB(enemy->sprite->format, 255, 255, 255));


	

	


	//Indique l'état et la direction de notre enemy
	enemy->direction = RIGHT;
	enemy->etat = IMMOBILE;

	//Réinitialise le timer de l'animation et la frame
	enemy->frame_timer = TIME_BETWEEN_2_FRAMES;
	enemy->frame_number = 0;

	/* Coordonnées de démarrage de notre enemies */
	enemy->x = 500 ;
	enemy->y = 250 ;

	

}


/*________________________________________________________________________________________*/



/*---fontion pour afficher l'enemy ---*//////////////////////////

void drawEnemy(Enemy enemy , Acteurs *acteurs)
{

/* Rectangle de destination à blitter */
SDL_Rect dest;
dest.x = enemy.x - acteurs->camera.x;
dest.y = enemy.y ;

SDL_Rect frame;
frame.x = enemy.frame_number*ENEMY_Width;
frame.y = 0;
frame.w = ENEMY_Width;
frame.h = ENEMY_Height;

/* Blitte notre enemy sur l'écran aux coordonnées x et y */

SDL_BlitSurface(enemy.sprite , &frame , acteurs->screen , &dest) ;
SDL_SetColorKey(enemy.sprite, SDL_SRCCOLORKEY, SDL_MapRGB(enemy.sprite->format, 255, 255, 255));
SDL_Delay(15);
	
}



/*________________________________________________________________________________________*/




void animationEnemy(Enemy* enemy, Acteurs acteurs)
{		
			if(enemy->frame_timer <= 0)
			{
				enemy->frame_number++;
				SDL_Delay(15);
			if(enemy->frame_number > MAXF_ENEMY-1)
				enemy->frame_number = 0;
			}
		else
		enemy->frame_timer--;
		SDL_Delay(15);	
	
}



/*________________________________________________________________________________________*/



void intelligence_artificielle(Enemy *enemy, Acteurs acteurs , Hero* hero)
{

int d ;  
		//if(hero.x < enemy.x)
  		d = enemy->x - hero->x - PLAYER_WIDTH;
  		

  		


		if(d<100 && d>0)
		{
				
			enemy->x -= 3 ;	
			animationEnemy(enemy,  acteurs);
                        
				
		}
		else
                {
                        initializeEnemy(enemy) ;
                        drawEnemy(*enemy , &acteurs) ;

                }



}
