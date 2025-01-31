## Todo: 
- [x] Learn about N64 hardware and software architecture (great starting point: https://www.copetti.org/writings/consoles/nintendo-64/)
* [ ] Read the source code
* [x] Research emulators and debugging tools, try to get them running
* [ ] See about loading stuff into binja? Might not be necessary, but might be helpful deeper into exploit developments
* [ ] Play the game! Gotta get familiar with what stuff actually does and how it's supposed to work

## Interesting things?
- PersistentCycleSceneFlags, persist through cycles, prob not useful.
- SysFlashrom_ReadData reads the save from dest <- src.
- `ovl_file_choose` Looks rather interesting ngl. Especially if we wanna find some form of ACE.
- `void FileSelect_SelectCopySource(GameState* thisx)`
	- Allow the player to select a file to copy or exit back to the main menu.
	- 
#### Save Context structure.
 SaveContext:
 ```c
 typedef struct SaveContext {
    /* 0x0000 */ Save save;
    /* 0x100C */ u8 eventInf[8]; // "event_inf"
    /* 0x1014 */ u8 unk_1014;    // "stone_set_flag"
    /* 0x1015 */ u8 bButtonStatus;
    /* 0x1016 */ u16 jinxTimer;
    /* 0x1018 */ s16 rupeeAccumulator;                       // "lupy_udct"
    /* 0x101A */ u8 bottleTimerStates[BOTTLE_MAX];           // See the `BottleTimerState` enum. "bottle_status"
    /* 0x1020 */ OSTime bottleTimerStartOsTimes[BOTTLE_MAX]; // The osTime when the timer starts. "bottle_ostime"
    /* 0x1050 */ u64 bottleTimerTimeLimits[BOTTLE_MAX]; // The original total time given before the timer expires, in centiseconds (1/100th sec). "bottle_sub"
    /* 0x1080 */ u64 bottleTimerCurTimes[BOTTLE_MAX];   // The remaining time left before the timer expires, in centiseconds (1/100th sec). "bottle_time"
    /* 0x10B0 */ OSTime bottleTimerPausedOsTimes[BOTTLE_MAX]; // The cumulative osTime spent with the timer paused. "bottle_stop_time"
    /* 0x10E0 */ u8 pictoPhotoI5[PICTO_PHOTO_COMPRESSED_SIZE]; // buffer containing the pictograph photo, compressed to I5 from I8
    /* 0x3CA0 */ s32 fileNum;                           // "file_no"
    /* 0x3CA4 */ s16 powderKegTimer;                    // "big_bom_timer"
    /* 0x3CA6 */ u8 unk_3CA6;
    /* 0x3CA7 */ u8 unk_3CA7;                           // "day_night_flag"
    /* 0x3CA8 */ s32 gameMode;                          // "mode"
    /* 0x3CAC */ s32 sceneLayer;                        // "counter"
    /* 0x3CB0 */ s32 respawnFlag;                       // "restart_flag"
    /* 0x3CB4 */ RespawnData respawn[RESPAWN_MODE_MAX]; // "restart_data"
    /* 0x3DB4 */ f32 entranceSpeed;                     // "player_wipe_speedF"
    /* 0x3DB8 */ u16 entranceSound;                     // "player_wipe_door_SE"
    /* 0x3DBA */ u8 unk_3DBA;                           // "player_wipe_item"
    /* 0x3DBB */ u8 retainWeatherMode;                  // "next_walk"
    /* 0x3DBC */ s16 dogParams;                         // OoT leftover. "dog_flag"
    /* 0x3DBE */ u8 envHazardTextTriggerFlags;          // "guide_status"
    /* 0x3DBF */ u8 showTitleCard;                      // "name_display"
    /* 0x3DC0 */ s16 nayrusLoveTimer;                   // remnant of OoT, "shield_magic_timer"
    /* 0x3DC2 */ u8 unk_3DC2;                           // "pad1"
    /* 0x3DC8 */ OSTime postmanTimerStopOsTime; // The osTime when the timer stops for the postman minigame. "get_time"
    /* 0x3DD0 */ u8 timerStates[TIMER_ID_MAX];  // See the `TimerState` enum. "event_fg"
    /* 0x3DD7 */ u8 timerDirections[TIMER_ID_MAX]; // See the `TimerDirection` enum. "calc_flag"
    /* 0x3DE0 */ u64 timerCurTimes[TIMER_ID_MAX]; // For countdown, the remaining time left. For countup, the time since the start. In centiseconds (1/100th sec). "event_ostime"
    /* 0x3E18 */ u64 timerTimeLimits[TIMER_ID_MAX]; // The original total time given for the timer to count from, in centiseconds (1/100th sec). "event_sub"
    /* 0x3E50 */ OSTime timerStartOsTimes[TIMER_ID_MAX]; // The osTime when the timer starts. "func_time"
    /* 0x3E88 */ u64 timerStopTimes[TIMER_ID_MAX];  // The total amount of time taken between the start and end of the timer, in centiseconds (1/100th sec). "func_end_time"
    /* 0x3EC0 */ OSTime timerPausedOsTimes[TIMER_ID_MAX]; // The cumulative osTime spent with the timer paused. "func_stop_time"
    /* 0x3EF8 */ s16 timerX[TIMER_ID_MAX]; // "event_xp"
    /* 0x3F06 */ s16 timerY[TIMER_ID_MAX]; // "event_yp"
    /* 0x3F14 */ s16 unk_3F14;             // "character_change"
    /* 0x3F16 */ u8 seqId;                 // "old_bgm"
    /* 0x3F17 */ u8 ambienceId;            // "old_env"
    /* 0x3F18 */ u8 buttonStatus[6];       // "button_item"
    /* 0x3F1E */ u8 hudVisibilityForceButtonAlphasByStatus; // if btn alphas are updated through Interface_UpdateButtonAlphas, instead update them through Interface_UpdateButtonAlphasByStatus "ck_fg"
    /* 0x3F20 */ u16 nextHudVisibility;  // triggers the hud to change visibility to the requested value. Reset to HUD_VISIBILITY_IDLE when target is reached "alpha_type"
    /* 0x3F22 */ u16 hudVisibility;      // current hud visibility "prev_alpha_type"
    /* 0x3F24 */ u16 hudVisibilityTimer; // number of frames in the transition to a new hud visibility. Used to step alpha "alpha_count"
    /* 0x3F26 */ u16 prevHudVisibility; // used to store and recover hud visibility for pause menu and text boxes "last_time_type"
    /* 0x3F28 */ s16 magicState;          // determines magic meter behavior on each frame "magic_flag"
    /* 0x3F2A */ s16 isMagicRequested;    // a request to add magic has been given "recovery_magic_flag"
    /* 0x3F2C */ s16 magicFlag;           // Set to 0 in func_80812D94(), otherwise unused "keep_magic_flag"
    /* 0x3F2E */ s16 magicCapacity;       // maximum magic available "magic_now_max"
    /* 0x3F30 */ s16 magicFillTarget;     // target used to fill magic "magic_now_now"
    /* 0x3F32 */ s16 magicToConsume;      // accumulated magic that is requested to be consumed "magic_used"
    /* 0x3F34 */ s16 magicToAdd;          // accumulated magic that is requested to be added "magic_recovery"
    /* 0x3F36 */ u16 mapIndex;            // set to enum DungeonSceneIndex when entering a dungeon related scene, or Map_GetMapIndexForOverworld on certain overworld scenes "scene_ID"
    /* 0x3F38 */ u16 minigameStatus;      // "yabusame_mode"
    /* 0x3F3A */ u16 minigameScore;       // "yabusame_total"
    /* 0x3F3C */ u16 minigameHiddenScore; // "yabusame_out_ct"
    /* 0x3F3E */ u8 unk_3F3E;             // "no_save"
    /* 0x3F3F */ u8 flashSaveAvailable;   // "flash_flag"
    /* 0x3F40 */ SaveOptions options;
    /* 0x3F46 */ u16 forcedSeqId;              // "NottoriBgm"
    /* 0x3F48 */ u8 cutsceneTransitionControl; // "fade_go"
    /* 0x3F4A */ u16 nextCutsceneIndex;        // "next_daytime"
    /* 0x3F4C */ u8 cutsceneTrigger;           // "doukidemo"
    /* 0x3F4D */ u8 chamberCutsceneNum;        // remnant of OoT "Kenjya_no"
    /* 0x3F4E */ u16 nextDayTime;              // "next_zelda_time"
    /* 0x3F50 */ u8 transFadeDuration;         // "fade_speed"
    /* 0x3F51 */ u8 transWipeSpeed;            // "wipe_speed"           transition related
    /* 0x3F52 */ u16 skyboxTime;               // "kankyo_time"
    /* 0x3F54 */ u8 dogIsLost;                 // "dog_event_flag"
    /* 0x3F55 */ u8 nextTransitionType;        // "next_wipe"
    /* 0x3F56 */ s16 worldMapArea;             // "area_type"
    /* 0x3F58 */ s16 sunsSongState;            // "sunmoon_flag"
    /* 0x3F5A */ s16 healthAccumulator;        // "life_mode"
    /* 0x3F5C */ s32 unk_3F5C;                 // "bet_rupees"
    /* 0x3F60 */ u8 screenScaleFlag;           // "framescale_flag"
    /* 0x3F64 */ f32 screenScale;              // "framescale_scale"
    /* 0x3F68 */ CycleSceneFlags cycleSceneFlags[120]; // Scene flags that are temporarily stored over the duration of a single 3-day cycle
    /* 0x48C8 */ u16 dungeonSceneSharedIndex; // similar to mapIndex, except values correspond to one of the four dungeons "scene_id_mix"
    /* 0x48CA */ u8 masksGivenOnMoon[27];     // bit-packed, masks given away on the Moon. "mask_mask_bit"
} SaveContext; // size = 0x48C8
```

#### Thing with the name selection:
```
@bug D_80814304 is accessed out of bounds when drawing the empty space character (value of 64). Under
//! normal circumstances it reads a halfword from D_80814384.
```

![[Pasted image 20240903191006.png]]

Okay, todo list for today, understand the save system and also the .fla (flash save) file format.

