# 1 to 1_RPG_Game_Dart_Android

## 1 to 1 RPG Game Overview
The following readme describes conducting a practice RPG Game using Dart. This game requires 3 functions: loading data, inputting data, and saving the Status. The functions and schedules are written as follows. And the game condition can be comprehended by the execution code so please look carefully throughout the code at the end. If you have any other questions regarding this program or code, please send me an email at nathanminsoooh@gmail.com. The game scope and conditions were previously designed by others, and it is a practice project as a learning material.

## Period of Time: 05/11/24(Tues) - 08/11/24(Fri)
* Tues  05 : The Game Design & Comprehension
* Wed   06 : Initiation of the Game Programming by DART
* Thurs 07 : On-going Dart Programming & Connection of Additional Files into main.dart file
* Fri   08 : close

## Designed Functions
* F1. Loading datas into main.dart file from an additional 2 data files
* F2. Inputting data from the terminal and Being able to follow the designed game conditions
* F3. Saving data from the gameplay

## Trouble Shooting & Improvement
* Issue-Description: Connection between files & paragraphs 
* Temporary Response: Creation of Files/Paragraphs
* Error: Muttable Connection 
* Solution: Muttable Connection towards the Immutable Complexity
* Future Plan: More Categorizing until Single Pieces
---------------- 

# Execution Code
## Creation of an additional 2 files before actual running code

1. Character.txt_Character File Creation & Data 
* 50,10,5

2. Monster.txt_Monster File Creation & Data
* Batman,30,20
* Spiderman,20,30
* Superman,30,10

----------

        import 'dart:io';
        import 'dart:math';

      class Character {
        String name;
        int health;
        int attackPower;
        int defensePower;

      Character(this.name, this.health, this.attackPower, this.defensePower);

      void attackMonster(Monster monster) {
        print('$name attacks ${monster.name}');
        monster.takeDamage(attackPower);
      }

      void defend() {
        print('$name defends and gains some health.');
        health += defensePower;
      }

      void showStatus() {
        print('$name - Health: $health, Attack: $attackPower, Defense: $defensePower');
      }

      void takeDamage(int damage) {
        health -= damage;
        if (health < 0) health = 0;
      }
        }

        class Monster {
          String name;
          int health;
          int maxAttackPower;
          int attackPower;

      Monster(this.name, this.health, this.maxAttackPower, int characterDefense)
          : attackPower = max(characterDefense, Random().nextInt(maxAttackPower + 1));

      void attackCharacter(Character character) {
        int damage = max(0, attackPower - character.defensePower);
        print('$name attacks ${character.name} for $damage damage.');
        character.takeDamage(damage);
      }

      void showStatus() {
        print('$name - Health: $health, Attack Power: $attackPower');
      }

      void takeDamage(int damage) {
        health -= damage;
            if (health < 0) health = 0;
          }
        }

        class Game {
          Character character;
          List<Monster> monsters;
          int defeatedMonsters;

      Game(this.character, this.monsters) : defeatedMonsters = 0;

      void startGame() {
        while (character.health > 0 && defeatedMonsters < monsters.length) {
          Monster monster = getRandomMonster();
          print('A wild ${monster.name} appeared!');
          while (monster.health > 0 && character.health > 0) {
            character.showStatus();
            monster.showStatus();
            print('Choose your action: (1) Attack (2) Defend');
            String? choice = stdin.readLineSync();
            if (choice == '1') {
              character.attackMonster(monster);
              } else if (choice == '2') {
              character.defend();
            }
            if (monster.health > 0) {
              monster.attackCharacter(character);
            }
          }
          if (character.health > 0) {
            defeatedMonsters++;
            monsters.remove(monster);
            print('You defeated the ${monster.name}');
            if (defeatedMonsters < monsters.length) {
              print('Do you want to fight the next monster? (y/n)');
              String? response = stdin.readLineSync();
              if (response == null || response.toLowerCase() != 'y') {
                break;
              }
            }
          }
        }
        if (character.health <= 0) {
          print('You have been defeated...');
          saveGameResult(false);
        } else {
          print('Congratulations! You have defeated all the monsters!');
          saveGameResult(true);
        }
      }

      Monster getRandomMonster() {
        return monsters[Random().nextInt(monsters.length)];
      }

      void saveGameResult(bool isVictory) {
        print("결과를 저장하시겠습니까? (y/n)");
        String? response = stdin.readLineSync();

        if (response != null && response.toLowerCase() == 'y') {
          try {
            final file = File('result.txt');
            String resultText =
                '캐릭터 이름: ${character.name}\n남은 체력: ${character.health}\n게임 결과: ${isVictory ? "승리" : "패배"}\n';
                file.writeAsStringSync(resultText);
                print("결과가 저장되었습니다.");
              } catch (e) {
                print('결과를 저장하는 데 실패했습니다: $e');
              }
            }
          }
        }

        Character loadCharacterStats() {
          try {
            final file = File('characters.txt');
            final contents = file.readAsStringSync();
            final stats = contents.split(',');
            if (stats.length != 3) throw FormatException('Invalid character data');

        int health = int.parse(stats[0]);
        int attack = int.parse(stats[1]);
        int defense = int.parse(stats[2]);

        String name = getCharacterName();
        return Character(name, health, attack, defense);
      } catch (e) {
        print('캐릭터 데이터를 불러오는 데 실패했습니다: $e');
        exit(1);
      }
        }

        List<Monster> loadMonsterStats(int characterDefense) {
          List<Monster> monsters = [];
          try {
            final file = File('monsters.txt');
            final lines = file.readAsLinesSync();
            for (var line in lines) {
              final stats = line.split(',');
              if (stats.length != 3) throw FormatException('Invalid monster data');

              String name = stats[0];
              int health = int.parse(stats[1]);
              int maxAttackPower = int.parse(stats[2]);

              monsters.add(Monster(name, health, maxAttackPower, characterDefense));
            }
          } catch (e) {
            print('몬스터 데이터를 불러오는 데 실패했습니다: $e');
            exit(1);
          }
  
         return monsters;
        }

        String getCharacterName() {
         RegExp validNamePattern = RegExp(r'^[a-zA-Z가-힣]+$');

         while (true) {
           print('캐릭터의 이름을 입력하세요: ');
           String? inputName = stdin.readLineSync();
    
           if (inputName != null && validNamePattern.hasMatch(inputName)) {
             return inputName;
           } else {
             print('유효하지 않은 이름입니다. 한글 또는 영문 대소문자만 사용할 수 있습니다.');
           }
         }
        }

        void main() { 
         Character hero = loadCharacterStats();

         List<Monster> monsters = loadMonsterStats(hero.defensePower);

         Game game = Game(hero, monsters);

         game.startGame();
        }
    





