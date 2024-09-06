Bro it's so THICC

## Sram Context:
```c
typedef struct SramContext {
    /* 0x00 */ u8* noFlashSaveBuf; // Never allocated memory
    /* 0x04 */ u8* saveBuf;
    /* 0x08 */ char unk_08[4];
    /* 0x0C */ s16 status;
    /* 0x10 */ u32 curPage;
    /* 0x14 */ u32 numPages;
    /* 0x18 */ OSTime startWriteOsTime;
    /* 0x20 */ s16 unk_20;
    /* 0x22 */ s16 unk_22;
    /* 0x24 */ s16 unk_24;
} SramContext; // size = 0x28
```
## Save Player Data:
```c
typedef struct SavePlayerData {
    /* 0x00 */ char newf[6];                          // "newf"               Will always be "ZELDA3 for a valid save
    /* 0x06 */ u16 threeDayResetCount;                // "savect"
    /* 0x08 */ char playerName[8];                    // "player_name"
    /* 0x10 */ s16 healthCapacity;                    // "max_life"
    /* 0x12 */ s16 health;                            // "now_life"
    /* 0x14 */ s8 magicLevel; // 0 for no magic/new load, 1 for magic, 2 for double magic "magic_max"
    /* 0x15 */ s8 magic; // current magic available for use "magic_now"
    /* 0x16 */ s16 rupees;                            // "lupy_count"
    /* 0x18 */ u16 swordHealth;                       // "long_sword_hp"
    /* 0x1A */ u16 tatlTimer;                         // "navi_timer"
    /* 0x1C */ u8 isMagicAcquired;                    // "magic_mode"
    /* 0x1D */ u8 isDoubleMagicAcquired;              // "magic_ability"
    /* 0x1E */ u8 doubleDefense;                      // "life_ability"
    /* 0x1F */ u8 unk_1F;                             // "ocarina_round"
    /* 0x20 */ u8 owlWarpId; // See `OwlWarpId`, "first_memory"
    /* 0x22 */ u16 owlActivationFlags;                // "memory_warp_point"
    /* 0x24 */ u8 unk_24;                             // "last_warp_pt"
    /* 0x26 */ s16 savedSceneId;                      // "scene_data_ID"
} SavePlayerData; // size = 0x28
```

## Save Info:
```c
typedef struct SaveInfo {
    /* 0x000 */ SavePlayerData playerData;
    /* 0x028 */ ItemEquips equips;
    /* 0x04C */ Inventory inventory;
    /* 0x0D4 */ PermanentSceneFlags permanentSceneFlags[120];
    /* 0xDF4 */ UNK_TYPE1 unk_DF4[0x54];
    /* 0xE48 */ u32 dekuPlaygroundHighScores[3];
    /* 0xE54 */ u32 pictoFlags0;                       // Flags set by `PictoActor`s if pictograph is valid
    /* 0xE58 */ u32 pictoFlags1;                       // Flags set by Snap_ValidatePictograph() to record errors; volatile since that function is run many times in succession
    /* 0xE5C */ u32 unk_E5C;
    /* 0xE60 */ u32 unk_E60;
    /* 0xE64 */ u32 alienInfo[7];                      // Used by EnInvadepoh to hold alien spawn times and how many aliens the player has killed
    /* 0xE80 */ u32 scenesVisible[7];                  // tingle maps and clouded regions on pause map. Stores scenes bitwise for up to 224 scenes even though there are not that many scenes
    /* 0xE9C */ u32 skullTokenCount;                   // upper 16 bits store Swamp skulls, lower 16 bits store Ocean skulls
    /* 0xEA0 */ u32 unk_EA0;                           // Gossic stone heart piece flags
    /* 0xEA4 */ u32 unk_EA4;
    /* 0xEA8 */ u32 unk_EA8[2];                        // Related to blue warps
    /* 0xEB0 */ u32 stolenItems;                       // Items stolen by Takkuri and given to Curiosity Shop Man
    /* 0xEB4 */ u32 unk_EB4;
    /* 0xEB8 */ u32 highScores[HS_MAX];
    /* 0xED4 */ u8 weekEventReg[100];                  // "week_event_reg"
    /* 0xF38 */ u32 regionsVisited;                    // "area_arrival"
    /* 0xF3C */ u32 worldMapCloudVisibility;           // "cloud_clear"
    /* 0xF40 */ u8 unk_F40;                            // "oca_rec_flag"                   has scarecrows song
    /* 0xF41 */ u8 scarecrowSpawnSongSet;              // "oca_rec_flag8"
    /* 0xF42 */ u8 scarecrowSpawnSong[128];
    /* 0xFC2 */ s8 bombersCaughtNum;                   // "aikotoba_index"
    /* 0xFC3 */ s8 bombersCaughtOrder[5];              // "aikotoba_table"
    /* 0xFC8 */ s8 lotteryCodes[3][3];                 // "numbers_table", Preset lottery codes
    /* 0xFD1 */ s8 spiderHouseMaskOrder[6];            // "kinsta_color_table"
    /* 0xFD7 */ s8 bomberCode[5];                      // "bombers_aikotoba_table"
    /* 0xFDC */ HorseData horseData;
    /* 0xFE6 */ u16 checksum;                          // "check_sum"
} SaveInfo; // size = 0xFE8
```
## Save Struct
```c
typedef struct Save {
    /* 0x00 */ s32 entrance;        // "scene_no"
    /* 0x04 */ u8 equippedMask;     // "player_mask"
    /* 0x05 */ u8 isFirstCycle;     // "opening_flag"
    /* 0x06 */ u8 unk_06;
    /* 0x07 */ u8 linkAge;          // "link_age"
    /* 0x08 */ s32 cutsceneIndex;   // "day_time"
    /* 0x0C */ u16 time;            // "zelda_time"
    /* 0x0E */ u16 owlWarpId;       // See `OwlWarpId` enum
    /* 0x10 */ s32 isNight;         // "asahiru_fg"
    /* 0x14 */ s32 timeSpeedOffset; // "change_zelda_time"
    /* 0x18 */ s32 day;             // "totalday"
    /* 0x1C */ s32 eventDayCount;   // "eventday"
    /* 0x20 */ u8 playerForm;       // "player_character"
    /* 0x21 */ u8 snowheadCleared;  // "spring_flag"
    /* 0x22 */ u8 hasTatl;          // "bell_flag"
    /* 0x23 */ u8 isOwlSave;
    /* 0x24 */ SaveInfo saveInfo;
} Save; // size = 0x100C
```
## Save Context 
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

## Start of game stuff with saves?

First view we see is where we select saves, the thing that does that is really silly goofy and is located in `src/overlays/ovl_file_choose/z_file_choose_NES.c`

We can delete a save (behavior of that in a sec), formulate a new save, which seems to take us into the name input portion, the name part we can only input an 8 character name. at least legally. However we can edit whatever we want lol.

One thing I wanna know is where the SRam is first read, since I can't find docs on that ;-;.

Other random question is like, all of the Ram is writable and executable right? Doesn't make sense if it's not?

Also, is NEWF a header for zelda flash saves? Gotta grab a hex editor and check.

- [ ] Check when a save actually gets made.

Also, Tatl is the fairy xd
Found where the gSaveContext is initialized lmao. Ig it's just chillin in memory globally allocated :shrug:
```c
void Setup_InitImpl(SetupState* this) {

    SysFlashrom_InitFlash();

    SaveContext_Init();

    Setup_InitRegs();

  

    STOP_GAMESTATE(&this->state);

    SET_NEXT_GAMESTATE(&this->state, ConsoleLogo_Init, sizeof(ConsoleLogoState));

}
```