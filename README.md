# SPACE GAME
Digital Portfolio

## DEVELOPMENT PROCESS - LOGS

### Andrew
**January 8th**
  
  - Finished sprite collection and graphics
  - Variables were used throughout all aspects and parts of the game, helped make it easier to code and change values/behaviours.
  - Using arrays for enemies and upgrades, allows for multiple values

**January 12th**

- Finished player and enemy movement, started on damage and wave progression.
- Using if statements for sectors and waves for enemy spawning. This fixed the enemies spawning indefinitely and allowed me to have different enemy counts for different waves.

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
- Adding finishing touches to game

**January 19th**

- Completed boss code
- Polishing and bug testing game

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
