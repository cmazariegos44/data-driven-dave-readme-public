![New Relic logo](https://newrelic.com/static-assets/images/logo/nr-logo-50vh.png)

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3.  [New Relic Instrumentation](#new-relic-instrumentation)
    - [Enabling distributed tracing](#enabling-distributed-tracing)
    - [Troubleshooting](#troubleshooting)
    - [Add More Custom New Relic Tracing](#add-more-custom-new-relic-tracing)
4.  [Build a Dashboard for Dangerous Dave App on New Relic One](#build-a-dashboard-for-dangerous-dave-app-on-new-relic-one)
    - [Build Dashboard Logo Tile](#build-dashboard-logo-tile)
    - [Build Dashboard 'Maximum Level Reached' Tile](#build-dashboard-maximum-level-reached-tile)
    - [Build Dashboard 'Top Score by Level' Tile](#build-dashboard-top-score-by-level-tile)
    - [Build Dashboard 'Games Played' Tile](#build-dashboard-games-played-tile)
    - [Build Dashboard 'Top Score Achieved' Tile](#build-dashboard-top-score-achieved-tile)
    - [Build Dashboard 'Loot' Tile](#build-dashboard-loot-tile)
    - [Build Dashboard 'Items Collected Over Time' Tile](#build-dashboard-items-collected-over-time-tile)
    - [Build Dashboard 'Real-time Item Collection Stats' Tile](#build-dashboard-real-time-collection-stats-tile)
- [Dangerous Dave Replica Credits](#dangerous-dave-replica-credits)

# Introduction
<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Introduction Content</summary>

If you don't already know why you need a monitoring and observability solution, it can be challenging to find an example that is illustrative, compelling, and engaging. There are literally thousands of "monitor this fake take-out food website" examples and - while they certainly show a valid real-world example of something you might want to monitor - they are usually a real yawner.

What if - and hear us out - you could play around with a monitoring solution AND PLAY A VIDEO GAME at the same time?!? WE KNOW, RIGHT?!?

That's what this example is all about. Install a game, set it up in New Relic, and then play the game to see your stats. 

Dangerous Dave was a classic 1980's side-scroller style game that many spent hours playing when we should have been doing productive work. Now we've turned the tables, making Dave help with our actual work.

The point of this project is two-fold: 

 1. To give folks a fun way to kick the tires on New Relic monitoring; 
 2. and to show how easy it is to instrument a custom application, capture and collect non-standard metrics, and display them in a meaningful way.

We hope you enjoy!

</details>

# Prerequisites
<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Prerequisites Content</summary>

  1. You'll need a New Relic account. The good news is that you can create a [free account here](https://newrelic.com/signup) (no credit card required).
  2. You'll need to clone down [Dangerous Dave Repo here](https://github.com/mwolfart/dangerous-dave).
  3. To compile the Dangerous Dave program, you must have Python 3 installed.
    - You will need to install the following packages using `pip` before starting the program. You may wish to install these packages in a [virtual environment](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/).
    - pygame (e.g. `pip3 install pygame`)
    - newrelic (e.g. `pip3 install newrelic`)
    
</details>


# New Relic Instrumentation

## Enabling distributed tracing
<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Enabling distributed tracing Content</summary>
1. To enable distributed tracing it is required to go to [New Relic One](https://one.newrelic.com/) -> once there the user should click on the "Add data" button located on the top right of New Relic One as seen on the screenshot below:

    <img src="readmeData/nr_one_add_data.png" alt="image" width="60%" height="60%">

2. Next the user should search for "Python" in the search bar available there and click on the Python icon visible in results as seen on the screenshot below. 

     <img src="readmeData/python_agent_search.png" alt="image" width="60%" height="60%">

3. This should open a view called "Name your application".

     <img src="readmeData/python_app_naming.png" alt="image" width="60%" height="60%">

4. Follow these 3 steps as seen on the screenshot below:
    1. Step 1: "Install the APM agent"
        - run the command in your projects terminal.
    2. Step 2: "Download your custom configuration file"
        - Place the downloaded file in your projects root directory. 
    3. Step 3: "Start your application's main module with the New Relic agent"
        - run the command stated in step 3 in your projects terminal.
        
    <img src="readmeData/python_installing_agent.png" alt="image" width="60%" height="60%">

5. Place this code in the main_fun.py file as seen on the screenshot below:  
    ```python main_fun.py
        import newrelic.agent
        newrelic.agent.initialize('newrelic.ini')
        @newrelic.agent.background_task()
    ```
     <img src="readmeData/nr_initial_code.png" alt="image" width="60%" height="60%">

6. Start your app with the command below: 
     ```python main_fun.py
        python main_fun.py
    ```

7. You should see the app displayed in New Relic One

    <img src="readmeData/app_display_nr1.png" alt="image" width="60%" height="60%">
</details>

## Troubleshooting

<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Troubleshooting</summary>

* During the step for **Testing the connection**, you may notice that the **On-host logs** fails. This is ok and is most likely due to an unsupported environment.
* You may also run into an issue where the agent fails to connect, as shown below. If the agent fails, but you still see data being sent to your New Relic account via the **APM & Services** for Relicstaurants, then you are good to go. If the agent fails, but you do not see data being sent to your New Relic account, then you may follow the troubleshooting steps [here](https://forum.newrelic.com/s/hubtopic/aAX8W0000008bSdWAI/nodejs-troubleshooting-framework-install).

<img width="806" alt="image" src="https://github.com/mchavez-newrelic/relicstaurants/assets/104166698/db0c9529-05a9-4249-927e-6056d52791c2">
</details>

## Add More Custom New Relic Tracing
<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Add More Custom New Relic Tracing Content</summary>

1. replace main_fun.py file with the code in the expand below
    <details>
    <summary>Click to expand</summary>
  
    ```python main_fun.py
            import newrelic.agent
            import math

            newrelic.agent.initialize('newrelic.ini') #This is required! [RLF]

            from classes import *
            from functional import *

            '''
            Interpic
            '''

            @newrelic.agent.background_task()
            def showTitleScreen(screen, tileset, ui_tiles):
                clock = pygame.time.Clock()
                
                # init graphics
                started_game = False
                titlepic_level = Map(1)
                dave_logo = AnimatedTile("davelogo", 0)
                overlay = Scenery("blacktile", 0)
                
                # clear screen on entering
                screen.clearScreen()
                
                # messages
                creator_text = "RECREATED BY ARTHUR, CATTANI AND MURILO"
                professor_text = "PROFESSOR LEANDRO K. WIVES"
                instr1_text = "PRESS SPACE TO START"
                instr2_text = "PRESSING ESC AT ANY MOMENT EXITS"
                
                while not started_game:
                    pygame.display.update()
                    
                    # print level and tiles
                    screen.setXPosition(14, titlepic_level.getWidth())
                    screen.printMap(titlepic_level, tileset)
                    screen.printTitlepicBorder(tileset)
                    screen.printTile(104, 0, dave_logo.getGraphic(ui_tiles))   
                    screen.printTile(0, BOTTOM_OVERLAY_POS, overlay.getGraphic(ui_tiles))
                    
                    # print text in center
                    screen.printTextAlignedInCenter(creator_text, 47)
                    screen.printTextAlignedInCenter(professor_text, 55)
                    screen.printTextAlignedInCenter(instr1_text, BOTTOM_OVERLAY_POS + 2)
                    screen.printTextAlignedInCenter(instr2_text, BOTTOM_OVERLAY_POS + 11)
                    
                    # if player pressed escape, exit game; space, start game
                    for event in pygame.event.get():
                        if event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
                            started_game = True  
                        elif event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
                            return True    # return 0 so we know player pressed escape

                    pygame.display.flip()
                    clock.tick(200)
                    
                # clear screen on exiting
                screen.clearScreen()
                    
                return False

            @newrelic.agent.background_task()
            def showInterpic(completed_levels, screen, GamePlayer, tileset, ui_tileset):
                clock = pygame.time.Clock()
                
                # init graphics
                interpic_level = Map("interpic")
                screen.setXPosition(0, interpic_level.getWidth())    
                screen.printMap(interpic_level, tileset)
                screen.clearBottomUi(ui_tileset)
                
                # init player
                (player_absolute_x, player_absolute_y) = interpic_level.initPlayerPositions(0, GamePlayer)
                GamePlayer.setCurrentState(STATE.WALK)
                GamePlayer.setDirectionX(DIRECTION.RIGHT)
                GamePlayer.setSpriteDirection(DIRECTION.RIGHT)

                # init messages
                intertext = "GOOD WORK! ONLY " + str(NUM_OF_LEVELS - completed_levels + 1) + " MORE TO GO!"
                last_level_text = "THIS IS THE LAST LEVEL!!!"
                finish_text = "YES! YOU FINISHED THE GAME!!"
                
                # keep moving the player right, until it reaches the screen boundary
                player_reached_boundary = (player_absolute_x >= screen.getUnscaledWidth())

                while not player_reached_boundary:
                    # if player pressed escape, quit game
                    for event in pygame.event.get():
                        if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
                            return True # return so we treat exiting externally
                            
                    # update player pos and animation
                    player_absolute_x = GamePlayer.movePlayerRight(player_absolute_x)
                    GamePlayer.updateAnimator()
                    
                    # update screen
                    screen.printMap(interpic_level, tileset)
                    screen.printOverlays(ui_tileset)
                    screen.printUi(ui_tileset, GamePlayer, completed_levels-1)
                    screen.printPlayer(GamePlayer, player_absolute_x, player_absolute_y, tileset)
                    
                    # print text accordingly to the number of completed levels
                    if completed_levels == NUM_OF_LEVELS + 1:
                        screen.printTextAlignedInCenter(finish_text, 54)
                    elif completed_levels == NUM_OF_LEVELS:
                        screen.printTextAlignedInCenter(last_level_text, 54)
                    else:
                        screen.printTextAlignedInCenter(intertext, 54)

                    player_reached_boundary = (player_absolute_x >= screen.getUnscaledWidth())
                    
                    pygame.display.flip()
                    clock.tick(200)
                    
                return False

            @newrelic.agent.background_task()
            def showWarpZone(completed_levels, screen, GamePlayer, tileset, ui_tileset):
                clock = pygame.time.Clock()
                
                # init graphics
                warp_level = Map("warp")
                screen.setXPosition(0, warp_level.getWidth())    
                screen.printMap(warp_level, tileset)
                screen.clearBottomUi(ui_tileset)
                
                # init player
                (player_absolute_x, player_absolute_y) = warp_level.initPlayerPositions(0, GamePlayer)
                GamePlayer.resetPosAndState()
                GamePlayer.setFallingState()
                GamePlayer.setGfxId(0)
                
                # keep moving the player right, until it reaches the screen boundary
                player_reached_bottom = (player_absolute_y >= screen.getUnscaledHeight())

                while not player_reached_bottom:
                    # if player pressed escape, quit game
                    for event in pygame.event.get():
                        if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
                            return True # return so we treat exiting externally
                    
                    player_absolute_y += 0.5
                    
                    # update screen
                    screen.printMap(warp_level, tileset)        
                    screen.printPlayer(GamePlayer, player_absolute_x, player_absolute_y, tileset)
                    screen.printOverlays(ui_tileset)
                    screen.printUi(ui_tileset, GamePlayer, completed_levels-1)

                    player_reached_bottom = (player_absolute_y >= screen.getUnscaledHeight())
                    
                    pygame.display.flip()
                    clock.tick(200)
                    
                return False
                
                
            @newrelic.agent.background_task()
            def getBonusMapping(current_level):
                if current_level == 2: return 6
                elif current_level == 5: return 2
                elif current_level == 6: return 9
                elif current_level == 7: return 10
                elif current_level == 8: return 6
                elif current_level == 9: return 7
                elif current_level == 10: return 1
                elif current_level == 1: return 11
                else: return 1
                
            @newrelic.agent.background_task()
            def showScores(screen, tileset):
                pass
                
            @newrelic.agent.background_task()
            def savePlayerScore(player_score, screen, tileset):
                pass
                    
            @newrelic.agent.background_task()
            def showCreditsScreen(screen, tileset):
                pass
            
            '''
            Game processing stuff
            '''
                
            '''
            Main
            '''

            def main():
                ##Init pygame
                pygame.init()
                game_screen = Screen(SCREEN_WIDTH, SCREEN_HEIGHT)
                
                ##Init tiles
                tileset, ui_tileset = load_all_tiles()
                game_open = True
                
                while game_open:
                    ##Show title screen
                    option = showTitleScreen(game_screen, tileset, ui_tileset)
                
                    #if player presses escape, close game
                    game_open = not option
                    
                    ##Init a player
                    GamePlayer = Player()
                
                    ##Init level and spawner
                    current_level_number = 1
                    current_spawner_id = 0

                    ##Available Keys
                    movement_keys = [pygame.K_UP, pygame.K_LEFT, pygame.K_RIGHT, pygame.K_DOWN]
                    inv_keys = [pygame.K_LCTRL, pygame.K_RCTRL, pygame.K_LALT, pygame.K_RALT]

                    ##Game processing
                    ended_game = False

                    while not ended_game:
                        # init clock and display
                        clock = pygame.time.Clock()
                        pygame.display.update()

                        # build the level and init screen and player positions
                        Level = Map(current_level_number)
                        (player_position_x, player_position_y) = Level.initPlayerPositions(current_spawner_id, GamePlayer)
                        
                        spawner_pos_x = Level.getPlayerSpawnerPosition(current_spawner_id)[0]
                        game_screen.setXPosition(spawner_pos_x - 10, Level.getWidth())

                        # UI Inits
                        score_ui = 0 #initial score. Everytime it changes, we update the ui
                        jetpack_ui = False
                        
                        # init other sprites
                        death_timer = -1
                        friendly_shot = 0

                        # level processing controller
                        ended_level = False

                        ## Level processing
                        while not ended_level:
                        
                            # get keys (inventory)
                            for event in pygame.event.get():
                                # stop moving
                                if event.type == pygame.KEYUP:
                                    # horizontally
                                    if event.key in [pygame.K_LEFT, pygame.K_RIGHT] and GamePlayer.getCurrentState() in [STATE.WALK, STATE.FLY, STATE.JUMP, STATE.CLIMB]:
                                        GamePlayer.clearXMovement()
                                    # vertically
                                    elif event.key in [pygame.K_UP, pygame.K_DOWN] and GamePlayer.getCurrentState() in [STATE.FLY, STATE.CLIMB]:
                                        GamePlayer.setVelocityY(0)
                                # hit a key
                                elif event.type == pygame.KEYDOWN:
                                    # quit game
                                    if event.key == pygame.K_ESCAPE:
                                        game_open = False
                                        ended_level = True
                                        ended_game = True
                                        # Record custom New Relic event [RLF]
                                        event_type = "GameComplete" 
                                        params = {'current_level': current_level_number, 'player_score': GamePlayer.score} 
                                        newrelic.agent.record_custom_event(event_type, params, application=application)
                                    # use something from the inventory
                                    elif event.key in inv_keys:
                                        if GamePlayer.inventoryInput(inv_keys.index(event.key)) and not friendly_shot:
                                            friendly_shot = Level.spawnFriendlyFire(GamePlayer.getSpriteDirection())
                                            friendly_shot_x, friendly_shot_y = player_position_x + GamePlayer.getDirectionX().value * WIDTH_OF_MAP_NODE, player_position_y

                            # get keys (movement)
                            pressed_keys = pygame.key.get_pressed()
                            key_map = [0,0,0,0]
                            for i, key in enumerate(movement_keys):
                                if pressed_keys[key]:
                                    key_map[i] = 1
                            GamePlayer.movementInput(key_map)

                            # update the player position in the level and treat collisions
                            if GamePlayer.getCurrentState() != STATE.DESTROY:
                                (player_position_x, player_position_y) = GamePlayer.updatePosition(player_position_x, player_position_y, Level, game_screen.getUnscaledHeight())
                                
                            # update friendly shot position, if there is one
                            if friendly_shot:
                                friendly_shot_x = friendly_shot.updatePosition(friendly_shot_x, friendly_shot_y, Level)
                                if (friendly_shot_x == -1):
                                    del friendly_shot
                                    friendly_shot = 0

                            # if the player ended the level, go on to the next
                            if GamePlayer.getCurrentState() == STATE.ENDMAP:
                                ended_level = True
                                break;
                            # if the player died, spawn death puff and respawn player (if he has enough lives)
                            elif GamePlayer.getCurrentState() == STATE.DESTROY:
                                if death_timer == -1:
                                    GamePlayer.takeLife()
                                    DeathPuff = AnimatedTile("explosion", 0)
                                    death_timer = 120
                                
                                player_position_y += 0.25
                                death_timer -= 1
                                
                                if death_timer == 0:
                                    death_timer = -1
                                    game_screen.setXPosition(Level.getPlayerSpawnerPosition(current_spawner_id)[0] - 10, Level.getWidth())
                                    del DeathPuff
                                    
                                    if (GamePlayer.resetPosAndState() != -1):
                                        (player_position_x, player_position_y) = Level.getPlayerSpawnerPosition(current_spawner_id)
                                        player_position_x *= WIDTH_OF_MAP_NODE
                                        player_position_y *= HEIGHT_OF_MAP_NODE
                                    else:
                                        ended_level = True
                                        ended_game = True
                                        # Record custom New Relic event [RLF]
                                        event_type = "GameComplete" 
                                        params = {'current_level': current_level_number, 'player_score': GamePlayer.score} 
                                        newrelic.agent.record_custom_event(event_type, params, application=application)
                                
                            # if the player is close enough to one of the screen boundaries, move the screen.
                            player_close_to_left_boundary = (player_position_x <= game_screen.getXPositionInPixelsUnscaled() + BOUNDARY_DISTANCE_TRIGGER)
                            player_close_to_right_boundary = (player_position_x >= game_screen.getXPositionInPixelsUnscaled() + game_screen.getUnscaledWidth() - BOUNDARY_DISTANCE_TRIGGER)
                            reached_level_left_boundary = (game_screen.getXPosition() <= 0)
                            reached_level_right_boundary = (game_screen.getXPosition() + game_screen.getWidthInTiles() > Level.getWidth())         

                            # move screen left
                            if player_close_to_left_boundary and not reached_level_left_boundary:
                                game_screen.moveScreenX(Level, -15, tileset, ui_tileset, GamePlayer, current_level_number)
                            # move screen right
                            elif player_close_to_right_boundary and not reached_level_right_boundary:
                                game_screen.moveScreenX(Level, 15, tileset, ui_tileset, GamePlayer, current_level_number)
                            # not moving (just update the screen)
                            else:
                                game_screen.printMap(Level, tileset)
                                
                                if friendly_shot:
                                    game_screen.printTile(friendly_shot_x - game_screen.getXPositionInPixelsUnscaled(), friendly_shot_y, friendly_shot.getGraphic(tileset))
                                    
                                    bullet_bypassed_screen_right_boundary = (friendly_shot_x >= game_screen.getXPositionInPixelsUnscaled() + game_screen.getUnscaledWidth())
                                    bullet_bypassed_screen_left_boundary = (friendly_shot_x <= game_screen.getXPositionInPixelsUnscaled())
                                    
                                    if bullet_bypassed_screen_right_boundary or bullet_bypassed_screen_left_boundary:
                                        del friendly_shot
                                        friendly_shot = 0
                                
                                if GamePlayer.getCurrentState() != STATE.DESTROY:
                                    # print player accordingly to screen shift
                                    game_screen.printPlayer(GamePlayer, player_position_x - game_screen.getXPositionInPixelsUnscaled(), player_position_y, tileset)
                                elif not ended_game:
                                    # print death puff accordingly to screen shift
                                    game_screen.printTile(player_position_x - game_screen.getXPositionInPixelsUnscaled(), player_position_y, DeathPuff.getGraphic(tileset))

                            # update UI
                            game_screen.printOverlays(ui_tileset)
                            game_screen.printUi(ui_tileset, GamePlayer, current_level_number)
                            
                            if not ended_level:
                                if GamePlayer.inventory["gun"] == 1:
                                    game_screen.updateUiGun(ui_tileset)
                                if GamePlayer.inventory["jetpack"] == 1 or jetpack_ui :
                                    game_screen.updateUiJetpack(ui_tileset, GamePlayer.inventory["jetpack"])
                                    jetpack_ui = True
                                if GamePlayer.inventory["trophy"] == 1:
                                    game_screen.updateUiTrophy(ui_tileset)
                                    
                            
                            if score_ui != GamePlayer.score:
                                game_screen.updateUiScore(GamePlayer.score, ui_tileset)
                                score_ui = GamePlayer.score                
                                
                            pygame.display.flip()
                            pygame.event.pump() 
                            clock.tick(200)

                        # Record custom New Relic event [RLF]
                        event_type = "LevelUp" 
                        params = {'current_level': current_level_number, 'player_score': GamePlayer.score} 
                        newrelic.agent.record_custom_event(event_type, params, application=application)

                        # Onto the next level
                        GamePlayer.clearInventory()
                        if (player_position_x == -2):
                            current_level_number = getBonusMapping(current_level_number)
                            current_spawner_id = 1
                        elif (current_spawner_id == 1):
                            current_level_number = getBonusMapping(current_level_number)
                            current_spawner_id = 0
                        else:
                            current_level_number += 1

                        GamePlayer.setCurrentLevelNumber(current_level_number)
                            
                        if current_level_number > NUM_OF_LEVELS and ended_level and not ended_game:
                            showCreditsScreen(game_screen, tileset)
                            ended_game = True
                            # Record custom New Relic event [RLF]
                            event_type = "GameComplete" 
                            current_level_number -= 1
                            params = {'current_level': current_level_number, 'player_score': GamePlayer.score} 
                            newrelic.agent.record_custom_event(event_type, params, application=application)
                        elif ended_level and current_spawner_id == 1:
                            option = showWarpZone(current_level_number, game_screen, GamePlayer, tileset, ui_tileset)
                            ended_game = option
                            game_open = not option
                        elif ended_level and not ended_game:
                            option = showInterpic(current_level_number, game_screen, GamePlayer, tileset, ui_tileset)
                            ended_game = option
                            game_open = not option
                            
                    savePlayerScore(GamePlayer.getScore(), game_screen, tileset)
                    showScores(game_screen, tileset)
                            
                pygame.quit()
                quit()

            application = newrelic.agent.register_application(timeout=5) # force New Relic agent registration [RLF]

            if __name__ == "__main__":
                main()

            newrelic.agent.shutdown_agent(timeout=2.5) # shutdown New Relic agent [RLF]

    ```
    </details>
2. then run 'python main_fun.py' in your terminal

</details>


# Build a Dashboard for Dangerous Dave App on New Relic One
In order to build the dashboards below you have to complete this step -> [Add More Custom New Relic Tracing](#add-more-custom-new-relic-tracing)
## Build Dashboard Logo Tile
<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Build Dashboard Logo Tile Content</summary>

1. Click 'Create a dashboard' as seen on the screenshot below

    <img src="readmeData/building_dashboard.png" alt="image" width="60%" height="60%">

2. Choose 'Create a new dashboard'
3. Name your dashboard (e.g. 'Dangerous Dave Game Stats')
4. Click 'Add a new chart'

    <img src="readmeData/add_new_chart.png" alt="image" width="60%" height="60%">

5. Click 'Add text, images, or links'

    <img src="readmeData/image_logo_dashboard.png" alt="image" width="60%" height="60%">

6. Add code below to the Markdown section then hit save

    `# Data Driven Dave ![New Relic logo](https://newrelic.com/static-assets/images/icons/avatar-newrelic.png)
    ![Data Driven Dave screenshot](https://raw.githubusercontent.com/devreldragon/data-driven-dave/main/Screenshots/game_screenshot-level01.png)`

    <img src="readmeData/image_markdown.png" alt="image" width="60%" height="60%">

7. End result should look like the below: 

    <img src="readmeData/dashboard_logo_result.png" alt="image" width="60%" height="60%">
</details>

## Build Dashboard 'Maximum Level Reached' Tile
<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Build Dashboard 'Maximum Level Reached' Tile Content</summary>

The data we will be using for this tile is a custom event created with the code below (you can see this code in your main_fun.py file if you pasted the code from these instructions -> [Add More Custom New Relic Tracing](#add-more-custom-new-relic-tracing))

```python main_fun.py
    # Record custom New Relic event [RLF]
        event_type = "GameComplete" 
        params = {'current_level': current_level_number, 'player_score': GamePlayer.score} 
        newrelic.agent.record_custom_event(event_type, params, application=application)
```

1. Click 'Add Widget'

    <img src="readmeData/step1_maximum_level_reached.png" alt="image" width="60%" height="60%">
2. Click 'Add a chart'

    <img src="readmeData/step2_maximum_level_reached.png" alt="image" width="60%" height="60%">
3. Place the code in step A in the section that looks like step B. 

    A.
         ``` SELECT max(current_level) AS 'Level' FROM LevelUp WHERE appName = 'Data Driven Dave' SINCE 1 month AGO```

    B. 
    
    <img src="readmeData/step3_maximum_level_reached.png" alt="image" width="60%" height="60%">

    C. 

    <img src="readmeData/step4_maximum_level_reached.png" alt="image" width="60%" height="60%">

    D. 
    - Chart Name = 'Maximum Level Reached'
    - Chart type = 'Bullet'
    - Limit = 10
    - Then hit 'Save'


    <img src="readmeData/step5_maximum_level_reached.png" alt="image" width="60%" height="60%">


7. End result should look like the below: 

    <img src="readmeData/step6_maximum_level_reached.png" alt="image" width="60%" height="60%">

</details>


## Build Dashboard 'Top Score by Level' Tile

<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Build Dashboard 'Top Score by Level' Tile Content</summary>
  
1. Click 'Add Widget'
2. Click 'Add a chart'
3. Place the code in step A in the section that looks like step B. 

    A.
         ```SELECT max(player_score) AS 'Top Score' FROM LevelUp WHERE appName = 'Data Driven Dave' FACET current_level AS 'Level' SINCE 1 month AGO```

    B. 
    
    <img src="readmeData/step3_maximum_level_reached.png" alt="image" width="60%" height="60%">
    
    C. 
    - Chart Name = 'Top Score by Level'
    - Chart type = 'Table'
    - Then hit 'Save'
</details>

## Build Dashboard 'Games Played' Tile

<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Build Dashboard 'Games Played' Tile Content</summary>

1. Click 'Add Widget'
2. Click 'Add a chart'
3. Place the code in step A in the section that looks like step B. 

    A.
         ```SELECT count(*) AS 'Games' FROM GameComplete WHERE appName = 'Data Driven Dave' SINCE 1 month ago```

    B. 
    
    <img src="readmeData/step3_maximum_level_reached.png" alt="image" width="60%" height="60%">
    
    C. 
    - Chart Name = 'Games Played'
    - Chart type = 'Billboard'
    - Then hit 'Save'
</details>

## Build Dashboard 'Top Score Achieved' Tile

<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Build Dashboard 'Top Score Achieved' Tile Content</summary>

1. Click 'Add Widget'
2. Click 'Add a chart'
3. Place the code in step A in the section that looks like step B. 

    A.
         ```SELECT max(player_score) FROM GameComplete WHERE appName = 'Data Driven Dave' SINCE 1 month ago```

    B. 
    
    <img src="readmeData/step3_maximum_level_reached.png" alt="image" width="60%" height="60%">
    
    C. 
    - Chart Name = 'Top Score Achieved'
    - Chart type = 'Billboard'
    - Then hit 'Save'
</details>

## Build Dashboard 'Loot' Tile

<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Build Dashboard 'Loot' Tile Content</summary>

1. Click 'Add Widget'
2. Click 'Add a chart'
3. Place the code in step A in the section that looks like step B. 

    A.
         ```SELECT count(*) AS '# Collected', sum(`item.score`) AS 'Total Value' FROM CollectedItem FACET if(`item.id` = 'items', item.type, item.id) AS 'Item Collected' SINCE 1 month AGO```

    B. 
    
    <img src="readmeData/step3_maximum_level_reached.png" alt="image" width="60%" height="60%">
    
    C. 
    - Chart Name = 'Loot'
    - Chart type = 'Table'
    - Then hit 'Save'
</details>

## Build Dashboard 'Items Collected' Tile

<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Build Dashboard 'Items Collected' Tile Content</summary>

1. Click 'Add Widget'
2. Click 'Add a chart'
3. Place the code in step A in the section that looks like step B. 

    A.
         ```SELECT count(*) AS '# Collected' FROM CollectedItem FACET if(`item.id` = 'items', item.type, item.id) AS 'Item Collected' SINCE 1 hour AGO```

    B. 
    
    <img src="readmeData/step3_maximum_level_reached.png" alt="image" width="60%" height="60%">
    
    C. 
    - Chart Name = 'Items Collected'
    - Chart type = 'Bar'
    - Then hit 'Save'
</details>

## Build Dashboard 'Items Collected Over Time' Tile

<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Build Dashboard 'Items Collected Over Time' Tile Content</summary>

1. Click 'Add Widget'
2. Click 'Add a chart'
3. Place the code in step A in the section that looks like step B. 

    A.
         ```SELECT count(*) AS '# Collected' FROM CollectedItem FACET if(`item.id` = 'items', item.type, item.id) AS 'Item Collected' SINCE 1 hour AGO```

    B. 
    
    <img src="readmeData/step3_maximum_level_reached.png" alt="image" width="60%" height="60%">
    
    C. 
    - Chart Name = 'Items Collected Over Time'
    - Chart type = 'Stacked Bar'
    - Then hit 'Save'
</details>

## Build Dashboard 'Real-time Item Collection Stats' Tile

<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Build Dashboard 'Real-time Item Collection Stats' Tile Content</summary>

1. Click 'Add Widget'
2. Click 'Add a chart'
3. Place the code in step A in the section that looks like step B. 

    A.
         ```SELECT count(*) FROM CollectedItem FACET if(`item.id` = 'items', item.type, item.id) SINCE 10 minutes AGO```

    B. 
    
    <img src="readmeData/step3_maximum_level_reached.png" alt="image" width="60%" height="60%">
    
    C. 
    - Chart Name = 'Real-time Item Collection Stats'
    - Chart type = 'Billboard'
    - Then hit 'Save'
</details>


# Dangerous Dave Replica Credits

<details>
  <summary style="background-color: #1DE783; color: black; padding: 10px;">Dangerous Dave Replica Credits Content</summary>

*(this is the original description you can find over on https://github.com/mwolfart/dangerous-dave) We remain deeply endebted to them for their effort to bring this classic game to life on the python platform! - Rachel and Leon)*

 - This project is a replica of the 1988 DOS game Dangerous Dave, made by John Romero. The project was built in Python along with a team of three students (Arthur Medeiros, Guilherme Cattani and me), as a course assignment.
 - The goal of the project was to study and practice the three types of programming paradigms: imperative, object-oriented and functional. To achieve this, we picked Python as a language since it can perform all three types of tasks in a fairly good way.

 </details>