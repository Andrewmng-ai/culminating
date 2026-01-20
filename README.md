# SPACE GAME
Digital Portfolio

## GAME IDEA
We wanted to make a rougelike game with a space theme to show our understanding of the course materials and also to challenge ourselves and have fun at the same time. As we both enjoy playing rougelike games, we wanted to try to make one ourselves and see how it would go. 

### TASKS
Player and enemy movement, health, UI, bosses, wave progression, sectors, graphics, pseudocode - ANDREW

Upgrades, enemy attacks, IPO charts, character selection, game states, animations, damage, stats - AIHAM

## DEVELOPMENT PROCESS - LOGS

### Andrew
**January 8th**
  
  - Finished sprite collection and graphics
  - Variables were used throughout all aspects and parts of the game, helped make it easier to code and change values/behaviours.
  - Using arrays for enemies and upgrades, allows for multiple values

// PLAYER STUFF
float playerX, playerY;
float playerSpeed = 10;
float playerMaxHealth = 100;
float playerHealth = 100;

float playerHitbox;

boolean movingLeft = false;
boolean movingRight = false;
boolean movingUp = false;
boolean movingDown = false;

int playerHitCooldown = 0;

// BULLET STUFF
ArrayList<Float> bulletX = new ArrayList<Float>();
ArrayList<Float> bulletY = new ArrayList<Float>();
ArrayList<Float> bulletAngle = new ArrayList<Float>();
float bulletSpeed = 30;
int shootDelay = 0;
int shootTimer = 0;
int bulletDamage = 20;

// ENEMY STUFF
ArrayList<Float> enemyX = new ArrayList<Float>();
ArrayList<Float> enemyY = new ArrayList<Float>();
ArrayList<Float> enemyHP = new ArrayList<Float>();
ArrayList<Integer> enemyType = new ArrayList<Integer>();


**January 12th**

- Finished player and enemy movement, started on damage and wave progression.
- Using if statements for sectors and waves for enemy spawning. This fixed the enemies spawning indefinitely and allowed me to have different enemy counts for different waves.

// enemy movement + repulsion
void enemyLogic() {
  for (int i = enemyX.size() - 1; i >= 0; i--) {
    float angleToPlayer = atan2(playerY - enemyY.get(i), playerX - enemyX.get(i));
    float vx = cos(angleToPlayer) * enemySpeed[enemyType.get(i)];
    float vy = sin(angleToPlayer) * enemySpeed[enemyType.get(i)];
    // repel enemies from other enemies
    for (int j = 0; j < enemyX.size(); j++) {
      if (i == j) continue;
      float dx = enemyX.get(i) - enemyX.get(j);
      float dy = enemyY.get(i) - enemyY.get(j);
      float d = sqrt(dx*dx + dy*dy);
      if (d < repulsionDistance && d > 0) {
        float push = (repulsionDistance - d) / d * 0.5;
        vx += dx * push;
        vy += dy * push;
      }
    }
    enemyX.set(i, enemyX.get(i) + vx);
    enemyY.set(i, enemyY.get(i) + vy);
    // player collision with enemies
    float dPlayer = dist(playerX, playerY, enemyX.get(i), enemyY.get(i));
    if (playerHitCooldown <= 0 && dPlayer < playerHitbox + enemyHitbox[enemyType.get(i)]) {
      playerHealth -= enemyDamage[enemyType.get(i)];
      playerHitCooldown = 20;
    }
    // bullet collision with enemies
    for (int b = bulletX.size() - 1; b >= 0; b--) {
      if (dist(bulletX.get(b), bulletY.get(b), enemyX.get(i), enemyY.get(i)) < enemyHitbox[enemyType.get(i)]) {
        enemyHP.set(i, enemyHP.get(i) - bulletDamage);
        bulletX.remove(b);
        bulletY.remove(b);
        bulletAngle.remove(b);
        break;
      }
    }
    // remove dead enemies
    if (enemyHP.get(i) <= 0) {
      enemyX.remove(i);
      enemyY.remove(i);
      enemyHP.remove(i);
      enemyType.remove(i);
    }
  }
}

**January 14th**

- Finished wave and sectors, started on boss code.

  for (int b = bulletX.size() - 1; b >= 0; b--) {
    if (dist(bulletX.get(b), bulletY.get(b), bossX, bossY) < bossHitbox) {
      bossHP -= bulletDamage;
      bulletX.remove(b);
      bulletY.remove(b);
      bulletAngle.remove(b);
    }
  }

- This for loop regulates bullet collision and damage for the bosses, checks if the bullet collides with the boss, if it does, then it removes HP based on how much the player's damage is. When the bullet collides with the bossHitbox (75), then it removes the bullet that has collided with it.

**January 16th**

- Finished most of boss code
- Boss 3 in progress
- Sectors completed
- Adding finishing touches to game

void nextSector() {
  if (currentSector >= 3) {
    gameOver = true;
    return;
  }

  currentSector++;

  // reset waves
  currentWave = 1;
  enemiesSpawnedThisWave = 0;
  waveActive = true;

  waveMultiplier = 1 + currentSector * 0.4;

  // sector based scaling
  if (currentSector == 2) {
    enemySpeed = new float[]{4, 3, 1};
    enemyMaxHP = new float[]{40, 80, 60};
    enemyDamage = new float[]{8, 16, 8};
    bossMaxHP = 2000;
    backgroundColor = color(90, 10, 10);
  }

  if (currentSector == 3) {
    enemySpeed = new float[]{5, 4, 1};
    enemyMaxHP = new float[]{80, 120, 70};
    enemyDamage = new float[]{10, 20, 10};
    bossMaxHP = 4000;
    backgroundColor = color(70, 0, 90);
  }
}

**January 19th**

- Completed boss code
- Polishing and bug testing game

// BOSS
boolean bossAlive = false;
float bossX, bossY;
float bossHP;
float bossMaxHP = 1000;
float bossHitbox = 75;

// BOSS 3 ATKS
// more homing
ArrayList<Float> boss3OrbX = new ArrayList<Float>();
ArrayList<Float> boss3OrbY = new ArrayList<Float>();
ArrayList<Float> boss3OrbAngle = new ArrayList<Float>();

int boss3OrbCount = 6;
float boss3OrbSpeed = 5;
float boss3OrbTurnSpeed = 0.08;
float boss3OrbHitbox = 14;
int boss3OrbDamage = 40;
int boss3OrbCooldown = 240;
int boss3OrbTimer = 0;

ArrayList<Integer> boss3OrbLife = new ArrayList<Integer>();
int boss3OrbHomingTime = 120;

// small slam
boolean boss3SlamActive = false;
boolean boss3SlamFiring = false;
int boss3SlamTimer = 0;
int boss3SlamBuildTime = 60;
int boss3SlamFireTime = 45;
int boss3SlamCooldown = 300;
int boss3SlamCount = 6;
float[] boss3SlamX = new float[boss3SlamCount];
float[] boss3SlamY = new float[boss3SlamCount];
float boss3SlamRadius = 90;
int boss3SlamDamage = 30;
boolean boss3SlamDidDamage = false;

// BOSS 2 ATKS
// ROCKS
ArrayList<Float> rockX = new ArrayList<Float>();
ArrayList<Float> rockY = new ArrayList<Float>();
float rockSpeed = 3;
float rockSize = 50;
int rockDamage = 30;
int rockSpawnCooldown = 60;
int rockSpawnTimer = 0;

// MISSION IMPOSSIBLE
boolean gridActive = false;
boolean gridFiring = false;
int gridTimer = 0;
int gridBuildTime = 90;
int gridFireTime = 60;
float gridSpacing = 120;

// SCREEN SLAM
boolean slamActive = false;
boolean slamLeft = true;
int slamTimer = 0;
int slamChargeTime = 90;
int slamDamage = 50;

// BOSS 1 ATKS
int bossAttackTimer = 0;

// LASER PHASES
int bossLaserTrackTime = 60;
int bossLaserWaitTime  = 45;
int bossLaserFireTime  = 90;

boolean bossLaserActive = false;
boolean bossLaserFiring = false;
boolean bossLaserDidDamage = false;

float bossLaserAngle = 0;

// LASER
int bossLaserTimer = 0;
int bossLaserPhase = 0;
float bossLaserWidth = 20;
int bossLaserDamage = 40;

// ROCKETS
int bossRocketCooldown = 300;
int bossRocketTimer = 0;
int bossRocketCount = 4;

ArrayList<Float> bossRocketX = new ArrayList<Float>();
ArrayList<Float> bossRocketY = new ArrayList<Float>();
ArrayList<Float> bossRocketAngle = new ArrayList<Float>();
ArrayList<Integer> bossRocketTrackTime = new ArrayList<Integer>();

int bossRocketTrackMax = 120;
float bossRocketSpeed = 4;
float bossRocketHitbox = 12;
int bossRocketDamage = 20;

// SUMMON
int bossSummonCooldown = 300;
int bossSummonTimer = 0;

// BOSS SPRITES
PImage boss1, boss2, boss3;

void bossLogic() {
  if (!bossAlive) return;

  // player bullets hit boss
  for (int b = bulletX.size() - 1; b >= 0; b--) {
    if (dist(bulletX.get(b), bulletY.get(b), bossX, bossY) < bossHitbox) {
      bossHP -= bulletDamage;
      bulletX.remove(b);
      bulletY.remove(b);
      bulletAngle.remove(b);
    }
  }

  // sector 1 attacks only
  if (currentSector == 1) {
    bossAttackTimer++;
    bossSummonTimer--;
    // rocket stuff
    bossRocketTimer--;
    if (bossRocketTimer <= 0) {
      float radius = bossHitbox + 20;
      float baseAngle = random(TWO_PI);
      for (int i = 0; i < bossRocketCount; i++) {
        float angleOffset = TWO_PI / bossRocketCount * i;
        float spawnAngle = baseAngle + angleOffset;
        float sx = bossX + cos(spawnAngle) * radius;
        float sy = bossY + sin(spawnAngle) * radius;
        bossRocketX.add(sx);
        bossRocketY.add(sy);
        bossRocketAngle.add(atan2(playerY - sy, playerX - sx) + random(-0.25, 0.25));
        bossRocketTrackTime.add(bossRocketTrackMax);
      }
      bossRocketTimer = bossRocketCooldown;
    }
    // laser
    if (bossLaserPhase == 0 && bossAttackTimer % 360 == 0) {
      bossLaserPhase = 1;
      bossLaserTimer = bossLaserTrackTime;
      bossLaserDidDamage = false;
    }
    if (bossLaserPhase != 0) {
      if (bossLaserPhase == 1) {
        bossLaserAngle = atan2(playerY - bossY, playerX - bossX);
        bossLaserTimer--;
        if (bossLaserTimer <= 0) {
          bossLaserPhase = 2;
          bossLaserTimer = bossLaserWaitTime;
        }
      } else if (bossLaserPhase == 2) {
        bossLaserTimer--;
        if (bossLaserTimer <= 0) {
          bossLaserPhase = 3;
          bossLaserTimer = bossLaserFireTime;
        }
      } else if (bossLaserPhase == 3) {
        bossLaserTimer--;
        float px = playerX - bossX;
        float py = playerY - bossY;
        float proj = px * cos(bossLaserAngle) + py * sin(bossLaserAngle);
        float distToBeam = abs(px * sin(bossLaserAngle) - py * cos(bossLaserAngle));
        if (!bossLaserDidDamage && proj > 0 && distToBeam < bossLaserWidth) {
          playerHealth -= bossLaserDamage;
          bossLaserDidDamage = true;
        }
        if (bossLaserTimer <= 0) bossLaserPhase = 0;
      }
    }
    // necromancer
    if (bossSummonTimer <= 0) {
      for (int i = 0; i < 3; i++) {
        enemyX.add(bossX + random(-80, 80));
        enemyY.add(bossY + random(60, 120));
        int type = int(random(3));
        enemyType.add(type);
        enemyHP.add(enemyMaxHP[type]);
        enemy2Cooldown.add(type == 2 ? 0 : -1);
      }
      bossSummonTimer = bossSummonCooldown;
    }
    // player collision
    if (playerHitCooldown <= 0 &&
      dist(playerX, playerY, bossX, bossY) < bossHitbox + playerHitbox) {
      playerHealth -= 30;
      playerHitCooldown = 30;
    }
    // rockets move
    for (int i = bossRocketX.size() - 1; i >= 0; i--) {
      float x = bossRocketX.get(i);
      float y = bossRocketY.get(i);
      float angle = bossRocketAngle.get(i);
      if (bossRocketTrackTime.get(i) > 0) {
        // tracking while rockets still active
        angle = atan2(playerY - y, playerX - x);
        bossRocketAngle.set(i, angle);
        bossRocketTrackTime.set(i, bossRocketTrackTime.get(i) - 1);
      }
      x += cos(angle) * bossRocketSpeed;
      y += sin(angle) * bossRocketSpeed;
      bossRocketX.set(i, x);
      bossRocketY.set(i, y);
      // hit player
      if (dist(x, y, playerX, playerY) < playerHitbox + bossRocketHitbox) {
        playerHealth -= bossRocketDamage;
        bossRocketX.remove(i);
        bossRocketY.remove(i);
        bossRocketAngle.remove(i);
        bossRocketTrackTime.remove(i);
        continue;
      }
      // off screen
      if (x < -50 || x > width + 50 || y < -50 || y > height + 50) {
        bossRocketX.remove(i);
        bossRocketY.remove(i);
        bossRocketAngle.remove(i);
        bossRocketTrackTime.remove(i);
      }
    }
  }
  // BOSS 2
  if (currentSector == 2) {
    bossAttackTimer++;
    // rocky
    rockSpawnTimer--;
    if (rockSpawnTimer <= 0) {
      rockX.add(random(width));
      rockY.add(-60f);
      rockSpawnTimer = rockSpawnCooldown;
    }
    for (int i = rockX.size() - 1; i >= 0; i--) {
      rockY.set(i, rockY.get(i) + rockSpeed);
      if (dist(rockX.get(i), rockY.get(i), playerX, playerY) < rockSize/2 + playerHitbox) {
        playerHealth -= rockDamage;
        rockX.remove(i);
        rockY.remove(i);
        continue;
      }
      if (rockY.get(i) > height + 80) {
        rockX.remove(i);
        rockY.remove(i);
      }
    }
    // mission impossible
    if (!gridActive && bossAttackTimer % 420 == 0) {
      gridActive = true;
      gridFiring = false;
      gridTimer = gridBuildTime;
    }
    if (gridActive) {
      gridTimer--;
      if (!gridFiring && gridTimer <= 0) {
        gridFiring = true;
        gridTimer = gridFireTime;
      }
      if (gridFiring) {
        for (float x = gridSpacing/2; x < width; x += gridSpacing) {
          if (abs(playerX - x) < bossLaserWidth) {
            playerHealth -= bossLaserDamage;
          }
        }
        for (float y = gridSpacing/2; y < height; y += gridSpacing) {
          if (abs(playerY - y) < bossLaserWidth) {
            playerHealth -= bossLaserDamage;
          }
        }
      }
      if (gridTimer <= 0 && gridFiring) {
        gridActive = false;
      }
    }
    // screen slam
    if (!slamActive && bossAttackTimer % 300 == 0) {
      slamActive = true;
      slamLeft = random(1) < 0.5;
      slamTimer = slamChargeTime;
    }
    if (slamActive) {
      slamTimer--;
      if (slamTimer <= 0) {
        boolean hit =
          (slamLeft && playerX < width/2) ||
          (!slamLeft && playerX > width/2);
        if (hit) playerHealth -= slamDamage;
        slamActive = false;
      }
    }
  }
  // BOSS 3
  if (currentSector == 3) {
    bossAttackTimer++;
    // more homing
    boss3OrbTimer--;
    if (boss3OrbTimer <= 0) {
      for (int i = 0; i < boss3OrbCount; i++) {
        float angle = TWO_PI / boss3OrbCount * i;
        boss3OrbX.add(bossX);
        boss3OrbY.add(bossY);
        boss3OrbAngle.add(angle);
        boss3OrbLife.add(boss3OrbHomingTime);
      }
      boss3OrbTimer = boss3OrbCooldown;
    }
    // move homing orbs
    for (int i = boss3OrbX.size() - 1; i >= 0; i--) {
      float x = boss3OrbX.get(i);
      float y = boss3OrbY.get(i);
      float a = boss3OrbAngle.get(i);
      int life = boss3OrbLife.get(i);
      if (life > 0) {
        a = atan2(playerY - y, playerX - x);
        boss3OrbLife.set(i, life - 1);
      }
      x += cos(a) * boss3OrbSpeed;
      y += sin(a) * boss3OrbSpeed;
      boss3OrbX.set(i, x);
      boss3OrbY.set(i, y);
      boss3OrbAngle.set(i, a);
      // hit player
      if (dist(x, y, playerX, playerY) < playerHitbox + boss3OrbHitbox) {
        playerHealth -= boss3OrbDamage;
        boss3OrbX.remove(i);
        boss3OrbY.remove(i);
        boss3OrbAngle.remove(i);
        boss3OrbLife.remove(i);
        continue;
      }
      // off screen
      if (x < -60 || x > width+60 || y < -60 || y > height+60) {
        boss3OrbX.remove(i);
        boss3OrbY.remove(i);
        boss3OrbAngle.remove(i);
        boss3OrbLife.remove(i);
      }
    }
    // slams yay
    boss3SlamTimer--;
    if (!boss3SlamActive && boss3SlamTimer <= 0) {
      boss3SlamActive = true;
      boss3SlamFiring = false;
      boss3SlamTimer = boss3SlamBuildTime;
      // random positions for slams
      for (int i = 0; i < boss3SlamCount; i++) {
        boss3SlamX[i] = random(100, width-100);
        boss3SlamY[i] = random(100, height-100);
      }
    }
    if (boss3SlamActive) {
      boss3SlamDidDamage = false;
      boss3SlamTimer--;
      // firing phase
      if (!boss3SlamFiring && boss3SlamTimer <= 0) {
        boss3SlamFiring = true;
        boss3SlamTimer = boss3SlamFireTime;
      }
      // damage player
      if (boss3SlamFiring && !boss3SlamDidDamage) {
        for (int i = 0; i < boss3SlamCount; i++) {
          if (dist(playerX, playerY, boss3SlamX[i], boss3SlamY[i]) < boss3SlamRadius) {
            playerHealth -= boss3SlamDamage;
            boss3SlamDidDamage = true;
            break;
          }
        }
      }
    }
    if (boss3SlamFiring && boss3SlamTimer <= 0) {
      boss3SlamActive = false;
      boss3SlamTimer = boss3SlamCooldown;
    }
  }

  // death
  if (bossHP <= 0 && bossAlive) {
    bossAlive = false;
    resetBoss();
    nextSector();
  }
}

### Aiham

**Day 1:** Making the character Reader (Jan 9th)
What concept did I implement?
Readers and the like, I implemented the ability for players to add in their own text files containing stats, names, and other variables, and added those values to an arraylist to be used at a later time.
This is necessary for player customization (and grades), as well as to allow us (the devs) to play through the game easily to test certain features

**Code: **
void checkForCharacters() {
  File filepath = new File(dataPath(""));
  File[] allFiles = filepath.listFiles();
  for (int i = 0; i < allFiles.length; i++) {
    if (allFiles[i].getName().toLowerCase().endsWith(".txt")) {
      numberofCharacters++;
      readers.add(createReader(allFiles[i]));
      characterStrength.add(0.);
      characterSize.add(0.);
      characterSpeed.add(0.);
      characterHp.add(0.);
      characterDefense.add(0.);
      characterFileNames.add(allFiles[i].getName());
      characterName.add("");
      characterAtkSpd.add(0.0);
      characterLuck.add(0.);
    }
  }
}

void readStats() {

  for (int i = 0; i < readers.size(); i++) {
    try {
      BufferedReader current = readers.get(i);
      String cline = current.readLine();
      while (cline != null) {
        if (cline.toLowerCase().contains("atk: ")) {
          int attack = parseInt(cline.replaceAll("[^\\d]", ""));
          characterStrength.set(i, (float)attack);
        }
        if (cline.toLowerCase().contains("def:")) {
          float def = parseInt(cline.replaceAll("[^\\d]", ""));
          characterDefense.set(i, def);
        }
        if (cline.toLowerCase().contains("size:")) {
          float size = parseInt(cline.replaceAll("[^\\d]", ""));
          characterSize.set(i, size);
        }
        if (cline.toLowerCase().contains("speed:")) {
          float speed = parseInt(cline.replaceAll("[^\\d]", ""));
          characterSpeed.set(i, speed);
        }
        if (cline.toLowerCase().contains("hp:")) {
          float hp = parseInt(cline.replaceAll("[^\\d]", ""));
          characterHp.set(i, hp);
        }
        if (cline.toLowerCase().contains("atk spd:")) {
          float atkspd = parseInt(cline.replaceAll("[^\\d]", ""));
          characterAtkSpd.set(i, atkspd);
        }
        if (cline.toLowerCase().contains("name:")) {
          String name = cline.substring(cline.indexOf(": ") + 2);
          characterName.set(i, name);
        }
        if (cline.toLowerCase().contains("sprite:")) {
          String sprite = cline.substring(cline.indexOf(": ") + 2);
          characterImage.add(i, loadImage(sprite));
        }
        cline = current.readLine();
      }
    }
    catch (IOException e) {
      e.printStackTrace();
    }
  }
}

**General Overview:**
Check for all files, then .txt files
Make a bufferedReader for each txt file
Read every line of the txt file, checking for character strings like Atk, Def, Spd, etc.
Added to the arrayList of all character stats (one per attack, defense, etc.)

**Challenges:**
Isolating the numbers for the stats (removing spaces and text)
Not accessing text files, would give errors saying not accessible and then gives a nullOverflow error
Character stat desync (same as previous)

**Solutions:**
Create stats either way (creates 0 variables or a standard player image if no image is selected)
W3Schools gives great code
Data folder access

**Day 2:** Making the character select (Jan 12)
What concept did I implement?
Added the ability to select characters based on the number of text files. This is needed for the added characters to be playable and easily accessible to new and returning players (nobody is playing this game longer than 5 minutes).

**Code:**
void characterSelectScreen() {
  if (currentCharacterSelected >= numberofCharacters)currentCharacterSelected -= numberofCharacters;
  if (currentCharacterSelected < 0)currentCharacterSelected = numberofCharacters + (currentCharacterSelected);
  int previouscharacter1 = currentCharacterSelected - 1;
  if (previouscharacter1 < 0)previouscharacter1 = numberofCharacters + (previouscharacter1 );
  int nextcharacter1 = currentCharacterSelected +1;
  if (nextcharacter1 >=  numberofCharacters)nextcharacter1 = (nextcharacter1) - numberofCharacters;
  int previouscharacter2 = currentCharacterSelected -2;
  if (previouscharacter2 < 0)previouscharacter2 = numberofCharacters + (previouscharacter2);
  int nextcharacter2 = currentCharacterSelected +2;
  if (nextcharacter2 >= numberofCharacters)nextcharacter2 =nextcharacter2 - numberofCharacters;
  pushMatrix();
  translate(0, -height/6);
  image(characterImage.get(currentCharacterSelected), width/2, height/2, width*64/400, width*64/400);
  image(characterImage.get(previouscharacter1), width/3, height/2, width*40/400, width*35/400);
  image(characterImage.get(nextcharacter1), width*2/3, height/2, width*40/400, width*35/400);
  if (characterImage.size() > 5){
  image(characterImage.get(previouscharacter2), width/5, height/2, width*30/400, width*20/400);
  image(characterImage.get(nextcharacter2), width*4/5, height/2, width*30/400, width*20/400);
  }
        textFont(title);
  textSize(48);
  textAlign(CENTER);
  fill(255);

  text(characterName.get(currentCharacterSelected), width/2, height*5.5/9 + 10);
  textAlign(LEFT);
      textFont(upgrade);
  textSize(24);

  text("Strength: " + characterStrength.get(currentCharacterSelected) + "\nDefense: " +
    characterDefense.get(currentCharacterSelected)
    + "\nHealth: " +
    characterHp.get(currentCharacterSelected)+ "\nSpeed: " +
    characterSpeed.get(currentCharacterSelected) + "\nAttack Speed: " + characterAtkSpd.get(currentCharacterSelected), width*2/5, height*11.5/18+ 10);
  pushMatrix();
  noFill();
  if (mouseX >= width/17 && mouseX <= width/8 && mouseY >= height/3 && mouseY <= height*2/3) {
    stroke(#dd0000);
    if (mousePressed && scrollCooldown <= 0) {
      currentCharacterSelected++;
      scrollCooldown = scrollMaxCooldown;
    }
  } else {
    stroke(#ff0000);
  }
  strokeWeight(8);
  beginShape();
  vertex(width/8, height/3);
  vertex(width/17, height/2);
  vertex(width/8, height*2/3);
  endShape();
  if (mouseX >= width*7/8 && mouseX <= width*16/17 && mouseY >= height/3 && mouseY <= height*2/3) {
    stroke(#dd0000);
    if (mousePressed && scrollCooldown <= 0) {
      currentCharacterSelected--;
      scrollCooldown = scrollMaxCooldown;
    }
  } else {
    stroke(#ff0000);
  }
  beginShape();
  vertex(width*7/8, height/3);
  vertex(width*16/17, height/2);
  vertex(width*7/8, height*2/3);
  endShape();
  popMatrix();
  popMatrix();
  startButton();
}

void mouseReleased() {
  scrollCooldown = 0;
}

void mouseWheel(MouseEvent e) {
  float scroll = e.getCount();
  currentCharacterSelected += scroll;
}

void startButton() {
  pushMatrix();
  stroke(#2553D8);
  strokeWeight(8);
  float buttonWidth =  width/4;
  float buttonHeight =  width*75/1000;
  if (mouseX >= width/2 - buttonWidth && mouseX <= width/2 + buttonHeight && mouseY >= height*4/5-buttonHeight && mouseY <= height*4/5+buttonHeight) {
    fill(#36509D);
    rect(width/2, height*4/5, buttonWidth, buttonHeight, 15);
    if (mousePressed) {
      playerHitbox = characterSize.get(currentCharacterSelected);
      playerDamage = characterStrength.get(currentCharacterSelected);
      playerDefense = characterDefense.get(currentCharacterSelected);
      playerSpeed = characterSpeed.get(currentCharacterSelected);
      playerMaxHealth = characterHp.get(currentCharacterSelected);
      playerImage = characterImage.get(currentCharacterSelected);
      shootDelay = characterAtkSpd.get(currentCharacterSelected);
      playerLuck = characterLuck.get(currentCharacterSelected);
      playerHealth = playerMaxHealth;
      restartGame();
      nextState = 2;
      isTransition = true;
    }
  } else {
    fill(#5079F0);
  }
  rect(width/2, height*4/5, buttonWidth, buttonHeight, 15);
  popMatrix();
  noStroke();
}

**Code Overview:**
- Creates 5 different variables for the previous 2 characters, the next 2, and the current character selected
- Displays stats based on the character stat arrayLists
- Added buttons and scrolling to change characters, scrolling isn’t used anywhere else so it is kept in this section
- Makes a button to start the game, and when pressed makes the current player stats equal to the stats of the current selected character (or their positions in the arraylist)

**Problem encountered:**
- If any stat is missing, it will cause a desync to happen in the stats (no crashes, but weird);

**Challenges:**
- Crashes for not having all stats and images
- Placement looking and feeling right

**Solutions:**
- Actually have the images
- Trial and error

**Day 3:** Making enemies (Jan 13)
What concept did I implement?
I added in the first enemy attack, this serves as the baseline for the rest of the enemies to work off of. It works by checking if the player is in range, then sets the attack warning to signify to the player where and when the attack will occur. Lastly, once the warning timer hits 0, the attack goes off, damaging any player inside. This is needed for the game to feel fun and refreshing, as just having enemies aimlessly run towards you feels very unnatural and just not fun to play against, instead, having different enemies with different attacks would lead to constantly different gameplay based on the amounts of certain enemies, and how they interact with each of the other kinds of enemies

**Code: **

void enemy1_1Attack() {
  for (int i = 0; i < enemyX.size(); i++) {
    if (enemyType.get(i) == 0) {
      if (dist(playerX, playerY, enemyX.get(i), enemyY.get(i)) <= (playerHitbox + enemyHitbox[enemyType.get(i)] + enemy1_1AttackRange)/3 && enemy1_1AttackCooldown.get(i) <= 0) {
        enemy1_1AttackWarning.set(i, enemy1_1AttackDur);
        enemy1_1AttackX.set(i, enemyX.get(i));
        enemy1_1AttackY.set(i, enemyY.get(i));
        enemy1_1Attacking.set(i, true);
        enemy1_1AttackCooldown.set(i, enemy1_1AttackCooldownMax);
      }
      if (enemy1_1Attacking.get(i) && enemy1_1AttackWarning.get(i) > 0) {
        enemy1_1AttackWarning.set(i, enemy1_1AttackWarning.get(i) - 1);
        fill(255, 0, 0, -((float)enemy1_1AttackWarning.get(i)/enemy1_1AttackDur) +1);
        circle(enemy1_1AttackX.get(i), enemy1_1AttackY.get(i), enemy1_1AttackRange);
        enemyX.set(i, enemy1_1AttackX.get(i));
        enemyY.set(i, enemy1_1AttackY.get(i));
      } else if (enemy1_1Attacking.get(i) && enemy1_1AttackWarning.get(i) <= 0) {
        enemy1_1Attacking.set(i, false);
        enemyHP.set(i, 0.);
        enemy1_1AttackWarning.set(i, enemy1_1AttackDur);
        fill(#FCC200);
        circle(enemy1_1AttackX.get(i), enemy1_1AttackY.get(i), enemy1_1AttackRange);
        if (dist(playerX, playerY, enemy1_1AttackX.get(i), enemy1_1AttackY.get(i)) <= (playerHitbox + enemyHitbox[enemyType.get(i)] + enemy1_1AttackRange)/2) {
          playerTakeDamage(enemyDamage[enemyType.get(i)]);
        }
      }
    }
  }
}

**Code Overview:**
- Checks if player is in range
- Does attack (sometimes sets an end position, sometimes an aoe attack)
- Does damage based on player defense
- Cons: Lots of variables (over 2 pages of copy paste)
- Pros: Working enemies

**Challenges:**
- Enemy variables desync
- No damage

**Solutions:**
- Initialize all variables when an enemy is spawned
- Make a damage function

**Day 4:** More enemies (and merging code) (Jan 14th)
What concept did I implement?
Same as the previous day, just with more variety and cooler and more complex attacks. This is just as needed as the previous code to make each enemy feel unique. If all enemies attack the same way, it would feel the same as if they were all running towards you. This was also when the attack code was merged with the enemy spawning and tracking code that Andrew made, finishing the enemy code section for now 

**Code:**

void enemy1_3Attack() {
  for (int i = 0; i < enemyX.size(); i++) {
    if (enemyType.get(i) == 2) {
      if (dist(playerX, playerY, enemyX.get(i), enemyY.get(i)) <= (playerHitbox+ enemy1_3AttackRange + enemyHitbox[2])/2) {
        if (enemy1_3AttackCooldown.get(i) <= 0) {
          enemy1_3AttackEndX.set(i, playerX);
          enemy1_3AttackEndY.set(i, playerY);
          enemy1_3BulletX.add(enemyX.get(i));
          enemy1_3BulletY.add(enemyY.get(i));
          float lengthDist = (enemyX.get(i) - playerX);
          float heightDist = (enemyY.get(i) - playerY);
          enemy1_3BulletAngle.add(atan2(heightDist, lengthDist));
          enemy1_3AttackCooldown.set(i, enemy1_3AttackCooldownMax);
        }
        for (int z = 0; z < enemy1_3BulletX.size(); z++) {
          if (enemy1_3BulletX.get(z) >= -50 && enemy1_3BulletX.get(z) <= width+50 && enemy1_3BulletY.get(z) >= -50 && enemy1_3BulletX.get(z) <= height+50) {
            float vx = cos(enemy1_3BulletAngle.get(z))*enemy1_3BulletSpeed;
            float vy = sin(enemy1_3BulletAngle.get(z))*enemy1_3BulletSpeed;
            enemy1_3BulletX.set(z, enemy1_3BulletX.get(z) - vx);
            enemy1_3BulletY.set(z, enemy1_3BulletY.get(z) - vy);
            fill(255, 0, 0);
            circle(enemy1_3BulletX.get(z), enemy1_3BulletY.get(z), enemy1_3BulletSize);
            if (dist(enemy1_3BulletX.get(z), enemy1_3BulletY.get(z), playerX, playerY) <= (playerHitbox+enemy1_3BulletSize)/2) {
              playerTakeDamage(enemyDamage[enemyType.get(i)]);
              enemy1_3BulletX.set(z, -400.);
              enemy1_3BulletY.set(z, -400.);
              enemy1_3BulletAngle.set(z, -400.);
            }
          }
        }
        if (dist(playerX, playerY, enemyX.get(i), enemyY.get(i)) <= (playerHitbox+ enemy1_3SafeRange + enemyHitbox[2])/2) {
          float lengthDist = playerX - enemyX.get(i);
          float heightDist = playerY - enemyY.get(i);
          float angle = atan2(heightDist, lengthDist);
          float vx = cos(angle)*enemySpeed[2];
          float vy = sin(angle)*enemySpeed[2];
          enemyX.set(i, enemyX.get(i) - vx*2);
          enemyY.set(i, enemyY.get(i) - vy*2);
        }
      }
    }
  }
}

void enemy1_2Attack() {

  for (int i = 0; i < enemyX.size(); i++) {
    if (enemyType.get(i) == 1) {
      if (dist(playerX, playerY, enemyX.get(i), enemyY.get(i)) <= (playerHitbox + enemyHitbox[enemyType.get(i)] + enemy1_2AttackRange)/3 && enemy1_2AttackCooldown.get(i) <= 0 && !enemy1_2Attacking.get(i)) {
        enemy1_2AttackWarning.set(i, enemy1_2AttackDur);
        enemy1_2AttackX.set(i, enemyX.get(i));
        enemy1_2AttackY.set(i, enemyY.get(i));
        enemy1_2Attacking.set(i, true);
        enemy1_2AttackCooldown.set(i, enemy1_2AttackCooldownMax);
        enemy1_2AttackEndX.set(i, playerX);
        enemy1_2AttackEndY.set(i, playerY);
      }
      if (enemy1_2Attacking.get(i) && enemy1_2AttackWarning.get(i) > 0) {
        enemy1_2AttackWarning.set(i, enemy1_2AttackWarning.get(i) - 1);
        fill(255, 0, 0, -((float)enemy1_2AttackWarning.get(i)/enemy1_2AttackDur) +1);
        float angle = atan2(enemy1_2AttackEndY.get(i)-enemyY.get(i), enemy1_2AttackEndX.get(i) - enemyX.get(i));
        pushMatrix();
        translate((enemy1_2AttackX.get(i)+enemy1_2AttackEndX.get(i))/2, (enemy1_2AttackY.get(i)+enemy1_2AttackEndY.get(i))/2);
        rotate(angle);
        rect(0, 0, enemy1_2AttackL, enemy1_2AttackW);
        popMatrix();
      }
    }
  }
}

**Overview:**
- The other enemies, same as last day
- Lots of new code but most is andrews (needed to merge enemy spawning and attacking system
- Cool attacks
- Loads more variables
- No desyncs

**Challenges: **
- Making sure no crashes come from the loads of variables and arraylists
- Had to clear all variables after wave ends
- Making sure enemies actually do attacks

**Solutions:** 
- Initialize variables for all enemies, regardless of type
- Fixing if statements with prints for each section

**Day 5:** UPGRADES (and changing enemy waves system) (Jan 15th)
What concept did I implement?
I added the ability for the player to get stronger alongside the enemies. For the enemies, this was just in the form of basic stat scaling at the end of each wave. For the player, this is where the fun starts, as there are different upgrades, each with different effects, ranging from crit chance, to dealing poison, to growing incredibly large. This allows the player to pick different build routes to go down, increasing replayability from 5 minutes to 20.

**Code:**

float upgradePadding = 20;
float upgradeMargin = 15;
float upgradeSize = 60;
ArrayList<Float> upgradeXpos = new ArrayList<Float>();
ArrayList<Float> upgradeYpos = new ArrayList<Float>();
ArrayList<Integer> upgradeRarity = new ArrayList<Integer>();
ArrayList<Integer> upgradeItem = new ArrayList<Integer>();
color rarityColor;
void doUpgradeTime() {
  if (upgradeTime) {
    while (upgradeXpos.size() < playerUpgradeCount) {
      upgradeXpos.add(0.);
      upgradeYpos.add(0.);
      upgradeRarity.add(0);
      upgradeItem.add(0);
    }
    drawUpgrades(playerUpgradeCount);
  }
}
int upgradeAmnt;

void drawUpgrades(float count) {
  boolean isOdd = count % 2 != 0;
  int rows = 1;
  while ((upgradeSize + upgradeMargin) * count / rows >= width - upgradePadding*2) {
    rows++;
  }
  for (int i = 0; i < count; i++) {
    if (upgradeXpos.get(i) == 0) {
      float series = ((count/2)* (count/2 - 1)*(upgradeSize+upgradeMargin))/2 - ((upgradeSize-upgradeMargin)/2);
      float xpos = width/2 + (upgradeSize+upgradeMargin)*i - (rows > 1 && i > count/2? series : 0) - (i%2==0? (upgradeMargin+upgradeSize)*1.5*i : (i > 1? (upgradeSize+upgradeMargin)*((i-1)/2) : 0)) - (!isOdd? (upgradeSize+upgradeMargin)/2 : 0);
      float ypos = (rows > 1? height/2 - (i <= count/2? (upgradeSize + upgradeMargin)/2 : -(upgradeSize + upgradeMargin)) : height/2);
      upgradeXpos.set(i, xpos);
      upgradeYpos.set(i, ypos);
      int rarityRand = (int)(random(0, 10+playerLuck/2));
      int rarity;
      int item;
      if (rarityRand >= 9) {
        item =  (int)random(0, legendaryUpgrades.length);
        rarity = 4;
        upgradeRarity.set(i, rarity);
        upgradeItem.set(i, item);
      } else if (rarityRand < 9 && rarityRand >= 7) {
        item =  (int)random(0, epicUpgrades.length);
        rarity = 3;
        upgradeRarity.set(i, rarity);
        upgradeItem.set(i, item);
      } else if (rarityRand < 7 && rarityRand >= 5) {
        item =  (int)random(0, rareUpgrades.length);
        rarity = 2;
        upgradeRarity.set(i, rarity);
        upgradeItem.set(i, item);
      } else if (rarityRand < 5) {
        item =  (int)random(0, commonUpgrades.length);
        rarity = 1;
        upgradeRarity.set(i, rarity);
        upgradeItem.set(i, item);
      }
    }
  }
  
  for (int i = 0; i < count; i++) {
    String[] upgradeNames;
    PImage[] upgradeImages = new PImage[(int)count];
    if (upgradeRarity.get(i) == 4) {
      rarityColor = (#FCD300);
      upgradeNames = legendaryUpgrades;
      upgradeImages[i] = (legendaryImages[(upgradeItem.get(i))]);
    } else if (upgradeRarity.get(i) == 3) {
      rarityColor = (#A300FC);
      upgradeNames = epicUpgrades;
      upgradeImages[i] = (epicImages[(upgradeItem.get(i))]);
    } else if (upgradeRarity.get(i) == 2) {
      rarityColor = (#0073FC);
      upgradeNames = rareUpgrades;
      upgradeImages[i] = (rareImages[(upgradeItem.get(i))]);
    } else if (upgradeRarity.get(i) == 1) {
      rarityColor = (#42FF36);
      upgradeNames = commonUpgrades;
      upgradeImages[i] = (commonImages[(upgradeItem.get(i))]);
    } else {
      rarityColor = (#42FF36);
      upgradeNames = commonUpgrades;
      upgradeImages[i] = (commonImages[(upgradeItem.get(i))]);
    }
    noFill();
    strokeWeight(8);
    if (mouseX >= upgradeXpos.get(i)-upgradeSize/2 && mouseX <= upgradeXpos.get(i) + upgradeSize/2 && mouseY <= upgradeYpos.get(i) + upgradeSize/2 && mouseY >= upgradeYpos.get(i) - upgradeSize/2) {
      if (mousePressed) {
        playerUpgradePick--;
        upgradeXpos.set(i, -400.);
        upgradeYpos.set(i, -400.);
        if (upgradeRarity.get(i) == 4 && upgradeItem.get(i) == 0){
          ouroborosPickup();
        }
        if (upgradeRarity.get(i) == 4 && upgradeItem.get(i) == 1){
          plagueMaskPickup();
        }
        if (upgradeRarity.get(i) == 4 && upgradeItem.get(i) == 2){
          weightsPickup();
        }
        if (upgradeRarity.get(i) == 4 && upgradeItem.get(i) == 3){
          pickupCrate();
        }
        if (upgradeRarity.get(i) == 3 && upgradeItem.get(i) == 0){
          luckyDicePickup();
        }
        if (upgradeRarity.get(i) == 3 && upgradeItem.get(i) == 1){
          glassCannonPickup();
        }
        if (upgradeRarity.get(i) == 3 && upgradeItem.get(i) == 2){
          squidPickup();
        }
        if (upgradeRarity.get(i) == 3 && upgradeItem.get(i) == 3){
          biggerBulletsPickup();
        }
        if (upgradeRarity.get(i) == 2 && upgradeItem.get(i) == 0){
          piercingPickup();
        }
        if (upgradeRarity.get(i) == 2 && upgradeItem.get(i) == 2){
          piercingPickup();
        }
        if (upgradeRarity.get(i) == 2 && upgradeItem.get(i) == 1){
          precisionPickup();
        }
        if (upgradeRarity.get(i) == 1 && upgradeItem.get(i) == 0){
          strongerMantlePickup();
        }
        if (upgradeRarity.get(i) == 1 && upgradeItem.get(i) == 1){
          afterburnersPickup();
        }
        if (upgradeRarity.get(i) == 1 && upgradeItem.get(i) == 2){
          largerHullPickup();
        }
      }
      pushStyle();
      fill(255);
      text(upgradeNames[upgradeItem.get(i)], width/2, height/3);
      popStyle();
      stroke(rarityColor);
      rect(upgradeXpos.get(i), upgradeYpos.get(i), upgradeSize + 10, upgradeSize + 10);
      image(upgradeImages[i], upgradeXpos.get(i), upgradeYpos.get(i), upgradeSize+10, upgradeSize+10);
    } else {
      stroke(rarityColor);
      rect(upgradeXpos.get(i), upgradeYpos.get(i), upgradeSize, upgradeSize);
      image(upgradeImages[i], upgradeXpos.get(i), upgradeYpos.get(i), upgradeSize, upgradeSize);
    }
  }
  
  if (playerUpgradePick <= 0) {
    if (currentWave == 3) {
      spawnBoss();
      upgradeTime = false;
      playerUpgradePick = playerUpgradePickMax;
      upgradeXpos.clear();
      upgradeYpos.clear();
      upgradeXpos.clear();
      return;
    } else {
      waveActive = true;
      currentWave++;
      waveMultiplier += 0.3;
      enemiesSpawnedThisWave = 0;
      upgradeTime = false;
      playerUpgradePick = playerUpgradePickMax;
      upgradeXpos.clear();
      upgradeYpos.clear();
      upgradeXpos.clear();
      upgradeScreenImages.clear();
    }
  }
}

**Code overview:**
- Creates upgrades if the current state is upgrading
- Renders upgrades
- Checks if player clicks on said upgrades, and if it does, based on the rarity and item, does the upgrade
