// e_helloworld.cc
// RDB 17 Aug 2010
// Based on e_helloworld.cc by JFL

#if 1

#include "GlobalVariables.h"
#include "qeGeometry.h"
#include "qec.h" // engine interface
#include "demoncore.h" // DePaul's core library

#define IMGPATH "../../../assets/img/"
#define SNDPATH "../../../assets/snd/"


enum { // unique-to-game ids
   GAMEID_ZERO, // always reserve zero
   GAMEID_MODE_TITLE, // always reserve zero
   GAMEID_MODE_GAME, // always reserve zero
   GAMEID_MISC=1, // reserve for general use
   GAMEID_ENEMY, // Rock
   GAMEID_PLAYER, // Player
   GAMEID_BULLET, // Bullet
};

// making sounds work
typedef struct {
   chr *name;
   uns flags;
   uns t;
   chr *path;
} sndDefRec;

sndDefRec snd[] = 
{
   {"playerLaser",M_SNDNEW_CH_5,0,
      SNDPATH"Laser3.wav"},

   {"explode",M_SNDNEW_CH_4,0,
   SNDPATH"Explosion2.wav"},

    {"noAmmo",M_SNDNEW_CH_4,0,
   SNDPATH"noAmmo.wav"},

   {"background",M_SNDNEW_CH_1|M_SNDNEW_LOOPING,0,
      SNDPATH"Music.wav"},
	 

   // special predefined sounds
   {"stop all",0x000|M_SNDNEW_PREDEFINED,0,"stopall"},

   {0,} // term
}; // snd[]
/*class GameMode : public qeListBase
{
public:
   chr name[16];
      virtual int load () {return 0;}
      virtual int unload () {return 0;}
      virtual int controlload () {return 0;}
      virtual int update () {return 0;}
      virtual int collide () {return 0;}
      virtual int draw () {return 0;}
      virtual void zap ();
}*/

/*class Controller : public qe
{
   static Controller *instance;
   Controller();
public:
   static Controller* InstanceNew();
   static Controller* InstanceGet();
   static void InstanceDelete();
};

class GameModeTitle : public GameMode
{
   float timeToChange;
   uns btnCount;
public:
   GameModeTitle();
   int load();
   int unload ();
   int update();
   int draw();
}; // class GameModeTitle

*/

/*int gameSetup()
{
Controller *cor = Controller::InstanceNew();
if (cor)
   cor->changeToModeNum(GAMEID_MODE_TITLE);
return 0;
}

GameModeTitle::GameModeTitle()
{
szfmt (this->name,sizeof(this->name),"title%d",++Game.id);
this->linkMakeNode (GAMEID_MODE_TITLE);
}

int GameModeTitle :: load()
{
if(qeImgNew("splash",MQEIMGNEW_LOAD,"../assets/img/Title.tga")<0)
   BRK();

this->timeToChange=qeTimeFrame() +3;
this->btnCount=gameCountAllButtons();
return 0;
}

int GameModeTitle:unload()
{
qeImgDelete("splash");
return 0;
}*/
/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
//Camera

Camera::Camera()
{
	// set camera parameters
	this->fovyHalfRad = DEGTORAD(45);
	this->nearClip = 1;
	this->farClip = 60;
	this->nearHeight = MathTanf(this->fovyHalfRad)*this->nearClip;

	// cam
	this->pos=Vec3(0, 0, 9);// position of the camera
	this->lookat=Vec3(0,0,0);// point camera is looking at
	this->up=Vec3(0,1,0);// up direction

	this->mat.identity();

} // Camera::Camera()

int Camera::apply()
{
	int r;

	//openGL states
	glDepthFunc(GL_LEQUAL);
	glEnable(GL_DEPTH_TEST);

	// current window size & aspect ratio
	if (qeGetWindowSize(&this->winWidth, &this->winHeight)<0)
		bret(-2);
	this->winWDivH=this->winWidth/this->winHeight;

	// set projection -- lens
	glMatrixMode(GL_PROJECTION);
	glLoadIdentity();
	glFrustum(-this->nearHeight*this->winWDivH, this->nearHeight*this->winWDivH,
		-this->nearHeight, this->nearHeight, this->nearClip,this->farClip);

	// set modelview -- position
	glMatrixMode(GL_MODELVIEW);
	glLoadIdentity();

	// calculate and set camera matrix
	qeCamLookAtM12f(this->mat.adr(), this->pos.adr(),
		this->lookat.adr(), this->up.adr()); // cam mat
	qeGLM12f(this->mat.adr()); // set matrix

	r=0;

BAIL:
	return r;

} // Camera::apply()

void Camera::zap()
{
	delete this;
}
///////////////////////////////////////////////////////////////////////////////
// Heads Up

// JFL 30 Sep 09
HeadsUp::HeadsUp()
{
   szfmt(this->name,sizeof(this->name),"hud%d",++Game.id);
} // HeadsUp::HeadsUp()

// JFL 30 Sep 09
void HeadsUp::zap()
{
   delete this;
} // HeadsUp::zap()

int HeadsUp::setUVTexCoord(chr *name, int number, float &u0, float &u1, float &v0, float &v1, int iconW, int iconH)
{
	int r;

	uns imgWidth, imgHeight;
	if (name == "timer")
	{
		if ((r=qeImgInfo("timer",&imgWidth, &imgHeight, NUL, NUL))<0)//|| (r=qeImgInfo("score",&imgWidth, &imgHeight, NUL, NUL))<0)
		{
			BRK();
			goto BAIL;
		}
	}
	else if (name == "score")
	{
		if ((r=qeImgInfo("score",&imgWidth, &imgHeight, NUL, NUL))<0)
		{
			BRK();
			goto BAIL;
		}

	}
	else if (name == "ammo")
	{
		if ((r=qeImgInfo("ammo",&imgWidth, &imgHeight, NUL, NUL))<0)
		{
			BRK();
			goto BAIL;
		}


	}

	u0  = (number&7);
	u0 *= iconW;
	v0  = (number>>3); 
	v0 *= iconH; 
	u1  = u0 + iconW; 
	v1  = v0 + iconH;

	u0 /= imgWidth-1;
	v0 /= imgHeight-1;
	u1 /= imgWidth-1;
	v1 /= imgHeight-1;

BAIL:
	return 0;
}

// NGS 31 Oct 12

int HeadsUp::update()
{

	//TODO: Update score
	return 0;
}

// NGS 31 Oct 12
int HeadsUp::drawTimer()
{
// set 2D camera
   //  (0,0,0)    ...    (1,0,0)
   //         (0.5,0.5,0)
   //  (0,1,0)    ...    (1,1,0)

	

   Game.numIconTimer = ((int)qeTimeFrame());

   // OpenGL states
   // bind image
   if(qeTexBind("timer")<0)
      BRK();
   glEnable(GL_TEXTURE_2D);
   glShadeModel(GL_SMOOTH);
   glPolygonMode(GL_FRONT,GL_FILL);
   //glColor4f(1,1,1,1);
   glColor3f(1,1,1);

   // setup texture mode and blending
   glTexEnvi(GL_TEXTURE_ENV,GL_TEXTURE_ENV_MODE,GL_MODULATE);
   glEnable(GL_BLEND);
   glBlendFunc(GL_SRC_ALPHA,GL_ONE_MINUS_SRC_ALPHA);

   // draw "score"
   glBegin(GL_QUADS);

   
	/////////// First number (One's place) ///////////
    setUVTexCoord("timer",(Game.numIconTimer%10), Game.u0, Game.u1, Game.v0, Game.v1, 25, 25);

   	glTexCoord2f(Game.u0, Game.v0); //top left in texture coordinates
	glVertex3f(0.25,0,0);
	

	glTexCoord2f(Game.u0, Game.v1); //bottom left in texture coordinates
	glVertex3f(0.25,0.06,0);
	

	glTexCoord2f(Game.u1, Game.v1); //bottom right in texture coordinates
	glVertex3f(0.29,0.06,0);
	

	glTexCoord2f(Game.u1, Game.v0); //top right in texture coordinates
	glVertex3f(0.29,0,0);

	/////////// Second number (tenth's place) ///////////

	setUVTexCoord("timer",((Game.numIconTimer/10)%10), Game.u0, Game.u1, Game.v0, Game.v1, 25, 25);
	
	glTexCoord2f(Game.u0, Game.v0); 
	glVertex3f(.20,0,0);

	glTexCoord2f(Game.u0, Game.v1);
	glVertex3f(.20,0.06,0);

	glTexCoord2f(Game.u1, Game.v1); 
	glVertex3f(.24,0.06,0);

	glTexCoord2f(Game.u1, Game.v0); 
	glVertex3f(0.24,0,0);

	///////////Third number (hundred's place) ///////////
	setUVTexCoord("timer",((Game.numIconTimer/100)%10), Game.u0, Game.u1, Game.v0, Game.v1, 25, 25);

	glTexCoord2f(Game.u0, Game.v0); 
	glVertex3f(.156,0,0);

	glTexCoord2f(Game.u0, Game.v1); 
	glVertex3f(.156,0.06,0);

	glTexCoord2f(Game.u1, Game.v1); 
	glVertex3f(.19,0.06,0);

	glTexCoord2f(Game.u1, Game.v0); 
	glVertex3f(0.19,0,0);

	/////////// 'Timer' Text ///////////
	setUVTexCoord("timer",10, Game.u0, Game.u1, Game.v0, Game.v1, 25, 25); //delete later: 10  for num, 85 width, ICON_HEGHT, for ARL
	
	glTexCoord2f((Game.u0)-0.022, Game.v0);
	glVertex3f(.04,0,0);

	glTexCoord2f((Game.u0)-0.022, Game.v1); 
	glVertex3f(.04,0.06,0);

	glTexCoord2f(Game.u1+0.2, Game.v1);
	glVertex3f(.15,0.06,0);

	glTexCoord2f(Game.u1+0.2, Game.v0); 
	glVertex3f(.15,0,0);

    glEnd();


   glDisable(GL_BLEND);
   glDisable(GL_TEXTURE_2D);

BAIL:
   return 0;
} // HeadsUp::drawUV()

int HeadsUp::drawScore()
{
	Mat12 scorePos;
	scorePos.identity();
	scorePos.xyz = Vec3(.15,0,0);

	if(qeTexBind("score")<0)
		BRK();

	glPushMatrix();
	qeGLM12f(scorePos.adr());
	

	glEnable(GL_TEXTURE_2D);
	glShadeModel(GL_SMOOTH);
	glPolygonMode(GL_FRONT,GL_FILL);
	//glColor4f(1,1,1,1);
	glColor3f(1,1,1);

	// setup texture mode and blending
	glTexEnvi(GL_TEXTURE_ENV,GL_TEXTURE_ENV_MODE,GL_MODULATE);
	glEnable(GL_BLEND);
	glBlendFunc(GL_SRC_ALPHA,GL_ONE_MINUS_SRC_ALPHA);

	// draw "score"
	glBegin(GL_QUADS);

   
	/////////// First number (One's place) ///////////
	setUVTexCoord("score",(Game.score%10), Game.u0, Game.u1, Game.v0, Game.v1, 25, 25);

	glTexCoord2f(Game.u0, Game.v0); //top left in texture coordinates
	glVertex3f(.77,0,0);
	

	glTexCoord2f(Game.u0, Game.v1); //bottom left in texture coordinates
	glVertex3f(.77,0.06,0);
	

	glTexCoord2f(Game.u1, Game.v1); //bottom right in texture coordinates
	glVertex3f(.80,0.06,0);
	

	glTexCoord2f(Game.u1, Game.v0); //top right in texture coordinates
	glVertex3f(.80,0,0);

	/////////// Second number (tenth's place) ///////////

	setUVTexCoord("score",((Game.score/10)%10), Game.u0, Game.u1, Game.v0, Game.v1, 25, 25);
	
	glTexCoord2f(Game.u0, Game.v0); 
	glVertex3f(.73,0,0);

	glTexCoord2f(Game.u0, Game.v1);
	glVertex3f(.73,0.06,0);

	glTexCoord2f(Game.u1, Game.v1); 
	glVertex3f(0.76,0.06,0);

	glTexCoord2f(Game.u1, Game.v0); 
	glVertex3f(0.76,0,0);

	
	///////////Third number (hundred's place) ///////////
	setUVTexCoord("score",((Game.score/100)%10), Game.u0, Game.u1, Game.v0, Game.v1, 25, 25);

	glTexCoord2f(Game.u0, Game.v0); 
	glVertex3f(.69,0,0);

	glTexCoord2f(Game.u0, Game.v1); 
	glVertex3f(.69,0.06,0);

	glTexCoord2f(Game.u1, Game.v1); 
	glVertex3f(.72,0.06,0);

	glTexCoord2f(Game.u1, Game.v0); 
	glVertex3f(0.72,0,0);

	/////////// 'Score' Text ///////////
	setUVTexCoord("score",9, Game.u0, Game.u1, Game.v0, Game.v1, 80, 25); //delete later: 10  for num, 85 width, ICON_HEGHT, for ARL
	
	glTexCoord2f((Game.u0)-0.14, Game.v0);
	glVertex3f(.57,0,0);

	glTexCoord2f((Game.u0)-0.14, Game.v1); 
	glVertex3f(.57,0.06,0);

	glTexCoord2f(Game.u1-0.1, Game.v1);
	glVertex3f(.69,0.06,0);

	glTexCoord2f(Game.u1-0.1, Game.v0); 
	glVertex3f(.69,0,0);
	
	glEnd();

	glDisable(GL_BLEND);
    glDisable(GL_TEXTURE_2D);
	glPopMatrix();

	return 0;
}

int HeadsUp::drawAmmo()
{
	Mat12 ammoPos;
	ammoPos.identity();
	ammoPos.xyz = Vec3(.15,0.9,0);

	if(qeTexBind("ammo")<0)
		BRK();

	glPushMatrix();
	qeGLM12f(ammoPos.adr());
	

	glEnable(GL_TEXTURE_2D);
	glShadeModel(GL_SMOOTH);
	glPolygonMode(GL_FRONT,GL_FILL);
	//glColor4f(1,1,1,1);
	glColor3f(1,1,1);

	// setup texture mode and blending
	glTexEnvi(GL_TEXTURE_ENV,GL_TEXTURE_ENV_MODE,GL_MODULATE);
	glEnable(GL_BLEND);
	glBlendFunc(GL_SRC_ALPHA,GL_ONE_MINUS_SRC_ALPHA);

	// draw "score"
	glBegin(GL_QUADS);

   
	/////////// First number (One's place) ///////////
	setUVTexCoord("ammo",(Game.ammo%10), Game.u0, Game.u1, Game.v0, Game.v1, 25, 25);

	glTexCoord2f(Game.u0, Game.v0); //top left in texture coordinates
	glVertex3f(.77,0,0);
	

	glTexCoord2f(Game.u0, Game.v1); //bottom left in texture coordinates
	glVertex3f(.77,0.06,0);
	

	glTexCoord2f(Game.u1, Game.v1); //bottom right in texture coordinates
	glVertex3f(.80,0.06,0);
	

	glTexCoord2f(Game.u1, Game.v0); //top right in texture coordinates
	glVertex3f(.80,0,0);

	/////////// Second number (tenth's place) ///////////

	setUVTexCoord("ammo",((Game.ammo/10)%10), Game.u0, Game.u1, Game.v0, Game.v1, 25, 25);
	
	glTexCoord2f(Game.u0, Game.v0); 
	glVertex3f(.73,0,0);

	glTexCoord2f(Game.u0, Game.v1);
	glVertex3f(.73,0.06,0);

	glTexCoord2f(Game.u1, Game.v1); 
	glVertex3f(0.76,0.06,0);

	glTexCoord2f(Game.u1, Game.v0); 
	glVertex3f(0.76,0,0);

	
	///////////Third number (hundred's place) ///////////
	setUVTexCoord("ammo",((Game.ammo/100)%10), Game.u0, Game.u1, Game.v0, Game.v1, 25, 25);

	glTexCoord2f(Game.u0, Game.v0); 
	glVertex3f(.69,0,0);

	glTexCoord2f(Game.u0, Game.v1); 
	glVertex3f(.69,0.06,0);

	glTexCoord2f(Game.u1, Game.v1); 
	glVertex3f(.72,0.06,0);

	glTexCoord2f(Game.u1, Game.v0); 
	glVertex3f(0.72,0,0);

	/////////// 'Score' Text ///////////
	setUVTexCoord("ammo",9, Game.u0, Game.u1, Game.v0, Game.v1, 80, 25); //delete later: 10  for num, 85 width, ICON_HEGHT, for ARL
	
	glTexCoord2f((Game.u0)-0.14, Game.v0);
	glVertex3f(.57,0,0);

	glTexCoord2f((Game.u0)-0.14, Game.v1); 
	glVertex3f(.57,0.06,0);

	glTexCoord2f(Game.u1-0.1, Game.v1);
	glVertex3f(.69,0.06,0);

	glTexCoord2f(Game.u1-0.1, Game.v0); 
	glVertex3f(.69,0,0);
	
	glEnd();

	glDisable(GL_BLEND);
    glDisable(GL_TEXTURE_2D);
	glPopMatrix();

	return 0;
}


 void HeadsUp::drawHUD()
{
	qefnSysCam2D();
	this->drawTimer();
	this->drawScore();
	this->drawAmmo();

}//HeadsUp::drawHUD()

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
//Collision checks
int BulletVsEnemyCol()
{
	int r;//, playID;
	Bullet* bullet;
	Enemy* enemy;
	qeListBase *n, *m;
	Vec3 combs;
	Vec3 combs2;

	for(n=Game.listOfBullets.nodeNext;n->nodeType;n=n->nodeNext)
	{
		if (n->nodeType != GAMEID_BULLET)
			continue;
		bullet=static_cast<Bullet*>(n);

		for(m=Game.listOfEnemies.nodeNext; m->nodeType;m = m->nodeNext)
	   {
		   if (m->nodeType != GAMEID_ENEMY)
			   continue;
		   enemy=static_cast<Enemy*>(m);
		   if (!(enemy->geo))
			   continue;

		   
		   if (bullet->pos.xyz.y > enemy->pos.xyz.y-0.5 && bullet->pos.xyz.y < enemy->pos.xyz.y-0.4 && bullet->pos.xyz.x > enemy->pos.xyz.x-0.5 && bullet->pos.xyz.x < enemy->pos.xyz.x+0.5)
		   {
			   enemy->geo = &geo_Explosion1;
			   
			   //qeSndPlayIdVol(qeSndPlay("explode"), 10);
			   qeSndPlay("explode");
			   enemy->dead = 1;
			   bullet->dead = 1;
			   Game.score += 10;
		   }

		   
		
		}//for
			
	}//for

	r=0;
	return 0;

}//BulletVsEnemyCol

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
// Enemy


Enemy::Enemy()
{
	this->dead = 0;
	this->timeOfLastUpdate=-1;
	this->linkMakeNode(GAMEID_ENEMY);
	Game.listOfEnemies.headAppend(this);
    szfmt(this->name,sizeof(this->name),"alienInstance%d",++Game.id); // unique name

	this->geo = &geo_Alien1;

	this->pos.identity();
}

int Enemy::update()
{
	

	float t;
	uns b;
//	float a;
	Mat9 rvel;
	
	if (this->timeOfLastUpdate < 0) // time not initialized
   {
      this->timeOfLastUpdate = qeTimeFrame(); 
   }
   
	this->xyzOld = this->pos.xyz;

	
	//this->acc = Vec3(0,0,0);
	 // update time
    t=this->timeOfLastUpdate;              // last update time
    this->timeOfLastUpdate=qeTimeFrame();  // current frame time
    t=this->timeOfLastUpdate-t;            // elapsed time
   
    //this->vel.x = 1;

	if (this->pos.xyz.x > MAX_X || this->pos.xyz.x < -MAX_X)
	{
		if (this->pos.xyz.x > MAX_X)
			this->pos.xyz.x = MAX_X;
		else
			this->pos.xyz.x = -MAX_X;

		this->vel.x *= -1;
		this->pos.xyz.y -= 1;
    }

	//this->shoot();

	if (t<.04)
		this->pos.xyz += this->vel*t;
	else 
		this->vel*0;
   
	return 0;
};

int Enemy::draw()
{
	if(this->dead)
	{
		this->zap();
		return 0;
	}

	float deg;
	deg = (this->rad*360.0)/PI;


	glPushMatrix();
	qeGLM12f(this->pos.adr());
	//glRotatef(deg,0,0,1);
	//glTranslatef(0,-1,0);
  /// glTranslatef(-10,-1,0);  <--- Starts better here
    //qeGeo2 *geo=&geo_Alien1; // pointer to tank base geometry
    qeDrawFaces(this->geo->verts,this->geo->faces,this->geo->numf);
	//glTranslatef(1.5,1.5,1.5);

	
	glPopMatrix();


	return 0;
}

void Enemy::zap()
{
	this->linkUnlink();
	delete this;
}

int Enemy::shoot()
{
	int r;
	Bullet *bullet;
	Vec3 dir;
	int soundId;

	if(!(bullet = new Bullet()))
		return(-1);

	bullet->pos.xyz = this->pos.xyz + Vec3(-.5,-1,0);

	bullet->vel += this->pos.rot.rotate(Vec3(0,-5,0));

	r=0;

BAIL:
	return r;
}
/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
// Player


Player::Player()
{
	this->pos.identity();
}

int Player::update()
{

	float t;
	uns b, c;
//	float a;
	Mat9 rvel;   
	
	this->acc = Vec3(0,0,0);
	 // update time
    t=this->timeOfLastUpdate;              // last update time
    this->timeOfLastUpdate=qeTimeFrame();  // current frame time
    t=this->timeOfLastUpdate-t;            // elapsed time
   
    this->vel.x = 0;

    b=qeInpButton(QEINPBUTTON_RIGHT);
	c=qeInpButton(QEINPBUTTON_KEY_D);

	if(b&1 || c&1) //&& (b!=Game.btnDown))
	{
		if (this->pos.xyz.x < MAX_X+.9)
		{
			this->vel.x = 5;
		}
	}
	
	b=qeInpButton(QEINPBUTTON_LEFT);
	c=qeInpButton(QEINPBUTTON_KEY_A);
	if(b&1 || c&1) //&& (b!=Game.btnDown))
	{
		if (this->pos.xyz.x > -MAX_X)
		{
		   this->vel.x = -5;
		}
	}
	
	int playID;
	b=qeInpButton(QEINPBUTTON_SPACEBAR);
	if((b&1)&&(Game.buttonShoot!=b))
	{
		if (!(Game.ammo <= 0))
		{
			Game.buttonShoot=b;
			//qeSndPlayIdVol(qeSndPlay("playerLaser"), 12);
			qeSndPlay("playerLaser");
			this->shoot();
			Game.ammo--;
		}
		else
		{
			Game.buttonShoot=b;
			//b=qeInpButton(QEINPBUTTON_SPACEBAR);
			//if((b&1)&&(Game.buttonShoot!=b))
				qeSndPlay("noAmmo");
		}
	}

   this->pos.xyz += this->vel*t;
   /*
    b=qeInpButton(QEINPBUTTON_SPACEBAR);
   if((b&1)&&(Game.buttonShoot!=b))
   {
      Game.buttonShoot=b;
      this->shoot();
   }*/
	return 0;
};

int Player::draw()
{
	// draw the base
      //glTranslatef(this->xyz.x,this->xyz.y,this->xyz.z); // position
      //deg = (this->yaBaseRad*180.0)/PI; // radians -> degrees
      //glRotatef(deg,0,1,0); // rotate the base
	//glPolygonMode(GL_FRONT,GL_LINE);

	float deg;
	deg = (this->rad*360.0)/PI;


	glPushMatrix();
	qeGLM12f(this->pos.adr());
	glRotatef(deg,0,0,1);
	glTranslatef(0,-1,0);

    qeGeo2 *geo=&geo_PlayerShip; // pointer to tank base geometry
    qeDrawFaces(geo->verts,geo->faces,geo->numf);
	//glTranslatef(1.5,1.5,1.5);

	
	glPopMatrix();


	return 0;
}

void Player::zap()
{
	delete this;
}

int Player::shoot()
{
	int r;
	Bullet *bullet;
	Vec3 dir;
	int soundId;

	if(!(bullet = new Bullet()))
		return(-1);

	bullet->pos.xyz = this->pos.xyz;

	bullet->vel += this->pos.rot.rotate(Vec3(0,5,0));

	r=0;

BAIL:
	return r;
}
///////////////////////////////////////////////////////////////////////////////
// Bullet


// JFL 17 Sep 07
// NGS 29 Oct 12
Bullet::Bullet()
{
	this->dead = 0;
    this->linkMakeNode(GAMEID_BULLET); // set node type
    Game.listOfBullets.headAppend(this); // link into list
    szfmt(this->name,sizeof(this->name),"bullet%d",++Game.id); // unique name
    this->pos.identity(); // reset matrix
    this->lastUpdateTime=qeTimeFrame(); // creation time
    this->timeToExpire=this->lastUpdateTime+3; // longer than it should be alive


} // Shot::Shot()

// JFL 23 Jul 09
void Bullet::zap()
{
   this->linkUnlink(); // unlink from whatever list it is in
   delete this;
} // Shot::zap()

// JFL 29 Jul 06
int Bullet::update()
{
   float t;

   // warning: multiple returns

   // update time
   t=this->lastUpdateTime;
   this->lastUpdateTime=qeTimeFrame();
   t=this->lastUpdateTime-t;

   // check if object has expired
   if(this->lastUpdateTime>=this->timeToExpire)
      return -1; // signal object has expired, remove safely

   // update velocity
   this->xyzOld = this->pos.xyz; // save old position for collision
   this->pos.xyz+=this->vel*t; // position change from velocity

   return 0;
} // Bullet::update()

// JFL 20 Sep 06
// ASW 19 May 13
int Bullet::draw(void)
{
   //glColor3f(1,0,0); // green
	if (this->dead)
		return -1;

   // draw
   glPushMatrix(); // save matrix
   qeGLM12f(this->pos.adr());
   glutWireSphere(.1,5,5);
   glPopMatrix(); // restore matrix

   return 0;
} // Bullet::draw()

///////////////////////////////////////////////////////////////////////////////
// Program

int gameSetup()
{
	MEMZ(Game);
	Game.listOfBullets.linkMakeHead(); // setup as head
	Game.listOfEnemies.linkMakeHead();

	Game.wOrg=800; //orignal layout width
	Game.hOrg=600; //original layout height

	Game.cam = new Camera();
	Game.player = new Player();
	//sndDefRec *sdr;
	//Game.isMusicPlaying = 0;
	Game.score = 0;
	Game.ammo  = 25;
	Game.shipsRemaining = 3;
 
	float velX, velZ;
	sndDefRec *sdr;

		if(qeGetWindowSize(&Game.wWindow,&Game.hWindow)<0)
			Game.wWindow=Game.hWindow=0;

		Game.xScale=Game.wWindow/Game.wOrg;
		Game.yScale=Game.hWindow/Game.hOrg;

		for(sdr=snd;sdr->name;sdr++)
	{
		if(qeSndNew(sdr->name,sdr->flags,sdr->t,sdr->path)<0)
			qePrintf("** qeSndNew() %s failed\n",sdr->name);
	} // for

	


	// add images
	if(qeImgNew("timer",0,IMGPATH"newTimer.tga")<0)
		BRK();


	if(qeImgNew("score",0,IMGPATH"newScore.tga")<0)
		BRK();


	if(qeImgNew("ammo",0,IMGPATH"newAmmo.tga")<0)
		BRK();

   int offset = 0;

   if (NUM_ENEMY > 12)
	   offset = NUM_ENEMY-12;
   for(int i=0; i < NUM_ENEMY;i++)
   {
	  
	   Game.enemyInstance[i] = new Enemy;
	   //velX = (qeRand01()*START_VEL)-(START_VEL*0.5);
	   //velZ = (qeRand01()*START_VEL)-(START_VEL*0.5);
       //enemy->vel.set(velX, 0, velZ);

	   Game.enemyInstance[i]->pos.xyz.set(i-offset,6,0);
	   Game.enemyInstance[i]->vel.set(START_VEL,0,0);
   } // for



  
	Game.player->pos.xyz.y = -4;
	
	return 0;
}

void gameFinal()
{
	qeListBase *n;

	if (Game.cam)
	{
		Game.cam->zap(); // controlled delete
		Game.cam = 0; // clear reference
	}
	Game.player->zap();

	 while((n=Game.listOfEnemies.nodeNext)&&n->nodeType)
   {
      if(n->nodeType==GAMEID_ENEMY)
         static_cast<Enemy*>(n)->zap();
      else
      {
         BRK();
         break;
      }
   } // while
	
  if(Game.hud)
   {
      Game.hud->zap(); // controlled delete
      Game.hud=0; // clear reference
   }

   while((n=Game.listOfBullets.headFirstUnlink()))
      static_cast<Bullet*>(n)->zap();

 
} // gameFinal()


int gameMainLoop()
{
   int playId;
   int i,r,a;

   //if(Game.isMusicPlaying == 0) //Will use this song for title screen instead
   //{
      //playId=qeSndPlay("background");
      //Game.isMusicPlaying = 1;
   //}

	Camera *cam;
	qeListBase *n, *m;
	Bullet *bullet;
	Enemy *enemy;
	
	// set 3D cam
	if((cam=Game.cam))
	{ 
		cam->apply();
	}

	 //Tank Update
    for(n=Game.listOfEnemies.nodeNext;n->nodeType;n=n->nodeNext)
   {
      if(n->nodeType!=GAMEID_ENEMY)
         continue; // skip
      enemy=static_cast<Enemy*>(n);
	  //enemy->overlapping=0; // clear flag each loop
      if(enemy->update()>0)
		  enemy->zap();
   } // for


	Game.player->update();

	  for(n=Game.listOfBullets.nodeNext;n->nodeType;n=m)
	  {
      m=n->nodeNext; // next node (in case we zap n)
      if(n->nodeType!=GAMEID_BULLET)
         continue; // skip
      bullet=static_cast<Bullet*>(n);
      if(bullet->update()<0)
         bullet->zap();
	  } // for

	BulletVsEnemyCol();
    
       // Enemy draw
   for(n=Game.listOfEnemies.nodeNext;n->nodeType;n=m)
   {
      m=n->nodeNext; // next node (in case we zap n)
      if(n->nodeType!=GAMEID_ENEMY)
         continue; // skip
      enemy =static_cast<Enemy*>(n);

	  glPolygonMode(GL_FRONT,GL_LINE); // prevents the mysterious color filling bug
      enemy->draw();
   } // for

	Game.player->draw();
	
	for(n=Game.listOfBullets.nodeNext;n->nodeType;n=n->nodeNext)
      static_cast<Bullet*>(n)->draw(); // cast list node to object & call

	Game.hud->drawHUD();

	return 0;
}




// RDB 17 Aug 2010
int qeMain(int argc,chr *argv[])
{
	qeOptionTurnOff(M_QEOPTION_AUTOCAM);
    // Print greeting
	
	if(!qeObjAddFnc(gameMainLoop))
   {
      BRK(); // error
      goto BAIL; // end
   }

   //qePrintf("Hello World\n");

   // turn control over to the engine

	  gameSetup();
	  qeForever();

BAIL:
   return 0;
} // qeMain()

#endif

