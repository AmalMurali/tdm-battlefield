/*------------------------============ZA PORPAVKU===========------------------------//
1. Popravi za svoj tim da se vidi na mapi  •NE•
2. Skloni nametag za protivnika •NE•
4. Popravi /round •NE•
5. Namesti /afk
6. Napravi Killcam
7. Prilagodi skriptu za trening
8. TEAMBALANCER PRIVREMENO UGASEN
9. Dodaj "[{F7B62A}ERROR{FFFFFF}]:You need to be admin to use this command!" na sve komande
//------------------------==================================------------------------*/
#include <a_samp>
#include <YSI\y_ini>
#include <zcmd>
#include <sscanf2>
#include <FloodControl>
#include <core>
#include <float>
//classes textdraws
new Text:Textdraw0; //onocrnonazad
new Text:Textdraw1; //natpis uzmi klasu
new Text:Textdraw2; //okvir1
new Text:Textdraw3; //okvir2
new Text:Textdraw4; //okvir3
new Text:Textdraw5; // support
new Text:Textdraw6;//assault
new Text:Textdraw7;//recon
new Text:Textdraw8;//selectable support
new Text:Textdraw9;//selectable assault
new Text:Textdraw10;//selectable recon
new gTeam[MAX_PLAYERS];
new Text:assisttext;
new Text:killtext;
new Text:headshottext;
//
new Text:crnapozadina;

new PlayerText:Level[MAX_PLAYERS];
new PlayerText:Rank[MAX_PLAYERS];
new PlayerText:XP[MAX_PLAYERS];
new PlayerText:zanivo[MAX_PLAYERS];

new Text:russkor;
new Text:usaskor;
new Text:TDtKills;
new Text:TDctKills;
new Text:Timeleft;
new Text:CurrentMap;
new Top3Objects[18];

//Team selection

new Text:Selection_TD[24];
new bool:Selection[MAX_PLAYERS];

//**************


//FPS
new FirstPerson[MAX_PLAYERS];
new FirstPersonObject[MAX_PLAYERS];
new bool:FPS[MAX_PLAYERS];
//

new minecount[MAX_PLAYERS]; //
new timer[MAX_PLAYERS]; //
new Text3D:minetext[MAX_PLAYERS];
new mine[MAX_PLAYERS][2];
new status[MAX_PLAYERS];
new minePickup[MAX_PLAYERS];

new bool:aHide[16];

new Text:TDEditor_TD[4];

/*new top_player = -1;
new top_player2 = 1;
new top_player3 = 1;
new top_kills  = 0;*/



//
#define loop(%1,%2) for (new %1 = 0; %1 < %2; %1++)
#define ROUND_MINUTES 5
#define FILTERSCRIPT
#define TEAM_GROVE_COLOR 0x00FF00AA
#define TEAM_BALLA_COLOR 0xFF00FFAA
#define COLOR_GREY 0xAFAFAFAA
#define COLOR_GREEN 0x33AA33AA
//#define COLOR_RED 0xAA3333AA
#define COLOR_RED 0xFF00002AA
#define COLOR_YELLOW 0xFFFF00AA
#define COLOR_WHITE 0xFFFFFFAA
//#define COLOR_BLUE 0x0066FF
#define COLOR_ORANGE 0xFF9900AA
#define COLOR_LIGHTBLUE 0x33CCFFAA
#define GOLD_COLOR 0xFFB700FF
#define COLOR_DARK_GREEN 0x33AA33FF
#define COLOR_BLUE 0x0033FFAA
#define COLOR_RINV 0xFF000000
#define COLOR_BINV 0x0000FF00
#define TEAM_RUS 9
#define TEAM_USA 10

new ReplyId[MAX_PLAYERS];
new warned[MAX_PLAYERS];
new Spawned[MAX_PLAYERS];
new Damaged[MAX_PLAYERS];
new Dead[MAX_PLAYERS];
new assistkill[MAX_PLAYERS] = INVALID_PLAYER_ID, Float: assist[MAX_PLAYERS];
new KillingSpree[MAX_PLAYERS];
new FirstBlood;
#define COL_WHITE "{FFFFFF}"
#define COL_RED "{F81414}"
#define COL_GREEN "{00FF22}"
#define COL_LIGHTBLUE "{00CED1}"
#define WHITE   0xFFFFFFFF
new szString[256];
forward warned1(playerid);
forward ShowTop3(playerid);
#define DIALOG_REGISTER 1
#define DIALOG_LOGIN 2
#define DIALOG_SUCCESS_1 3
#define DIALOG_SUCCESS_2 4
#define DIALOG_STATS 5
#define DIALOG_ROUND 6
#define DIALOG_RULES 7
#define DIALOG_COMMAND 9
#define DIALOG_LVL_1 10
#define DIALOG_LVL_2 11
#define DIALOG_LVL_3 12
#define DIALOG_LVL_4 13
#define DIALOG_LVL_5 14
#define DIALOG_UPDATES 15
#define DIALOG_ADMINS 16
new Notify[MAX_PLAYERS];

new RandomMSG[][]=
{
	"[{F7B62A}HELP{FFFFFF}]: Use /help for help dialog",
    "[{F7B62A}HELP{FFFFFF}]: If you want to change class do /class",
    "[{F7B62A}HELP{FFFFFF}]: If you see cheater or rulebreaker use /report [ID] [Reason]",
    "[{F7B62A}HELP{FFFFFF}]: Do not break rules or you will be banned!",
    "[{F7B62A}HELP{FFFFFF}]: For server rules type /rules",
    "[{F7B62A}HELP{FFFFFF}]: If you like our server,add it to favorites!",
    "[{F7B62A}HELP{FFFFFF}]: For news and updates type /update(s)",
    "[{F7B62A}HELP{FFFFFF}]: If you want to see server command, type /cmds "
};
#define PATH "/Users/%s.ini"

#define BODY_PART_HEAD 9

#define MAX_MAPS 1000

enum pInfo
{
    pPass,
    pScore,
    pCash,
    pAdmin,
    pKills,
    pDeaths,
    pRoundkills,
    pBanned,
    pHour,
    pMin,
    pSec,
    RegisterTime,
    RegisterDate,
	pLevel,
	pRank,
	pXP
}
new PlayerInfo[MAX_PLAYERS][pInfo];
enum mInfo
{
	mName[32],
	mFscriptName[32],
	Float:mCTSpawnX,
	Float:mCTSpawnY,
	Float:mCTSpawnZ,
	Float:mTSpawnX,
	Float:mTSpawnY,
	Float:mTSpawnZ,
}



new MapInfo[MAX_MAPS][mInfo];

forward LoadUser_data(playerid,name[],value[]);
public LoadUser_data(playerid,name[],value[])
{
    INI_Int("Password",PlayerInfo[playerid][pPass]);
    INI_Int("Admin",PlayerInfo[playerid][pAdmin]);
    INI_Int("Kills",PlayerInfo[playerid][pKills]);
    INI_Int("Deaths",PlayerInfo[playerid][pDeaths]);
    INI_Int("Score", PlayerInfo[playerid][pScore]);
    INI_Int("Banned",PlayerInfo[playerid] [pBanned]);
    INI_Int("Hours",PlayerInfo[playerid][pHour]);
    INI_Int("Minutes",PlayerInfo[playerid][pMin]);
    INI_Int("Seconds",PlayerInfo[playerid][pSec]);
    INI_Int("Level",PlayerInfo[playerid][pLevel]);
    INI_Int("Rank",PlayerInfo[playerid][pRank]);
    INI_Int("XP",PlayerInfo[playerid][pXP]);
	return 1;
}
strlower(string[])
{
    new tempstring[MAX_PLAYER_NAME];

    for(new i; i<strlen(string); i++) tempstring[i] = tolower(string[i]);

    return tempstring;
}

stock UserPath(playerid)
{
    new string[128],playername[MAX_PLAYER_NAME];
    GetPlayerName(playerid,playername,sizeof(playername));
    format(string,sizeof(string),PATH,strlower(playername));
    return string;
}

stock udb_hash(buf[]) {
    new length=strlen(buf);
    new s1 = 1;
    new s2 = 0;
    new n;
    for (n=0; n<length; n++)
    {
       s1 = (s1 + buf[n]) % 65521;
       s2 = (s2 + s1)     % 65521;
    }
    return (s2 << 16) + s1;
}

new Text:WinnerBackground1;
new Text:WinnerBackground2;
new Text:Winningteam;
new Text:MVP;
new Text:PlayerHealth[MAX_PLAYERS];

new timeleft = ROUND_MINUTES;
new seconds=0;
new maptimer;
new ctKills = 0;
new tKills = 0;

new MAP = 0;
new LastFS[32];
new mapid;

new bool:SPAWNED[MAX_PLAYERS];
new bool:AntiSpawnKill[MAX_PLAYERS];
new AntiSpawnKillTimer[MAX_PLAYERS];

//obrisi mislim da jet dmg inidikator
new hit[MAX_PLAYERS];
new Float:damage[MAX_PLAYERS];
new timerdamage[MAX_PLAYERS];
new PlayerText:Indicator[MAX_PLAYERS];
new PlayerText:IndicatorBox[MAX_PLAYERS];


forward Second();
forward ChangeMap();
forward FloodCheck(playerid);

main()
{
        print("\n------------------------------------------");
        print("Battlefield 3 loaded.");
        print("------------------------------------------\n");
}


public OnGameModeInit()
{
		//Maps RUS is first coords.
		AddMap("Container", "container",-10.63,459.51,2.18, 94.6138,407.2856,2.0687);
		AddMap("Valley","valley",507.3112,-2455.5693,9.0989,543.0502,-2155.3337,5.8720);
		AddMap("Chinatown", "chinatown",-129.1752,-3924.2021,103.3593, -211.9363,-3721.5669,103.2922);
		AddMap("Aftermath", "aftermath", 371.7985,588.0425,53.9511, 155.0541,364.7326,55.8149);
		AddMap("Desert", "desert",  1011.6662,714.0105,85.0050, 1374.6812,730.2754,83.5891);
		AddMap("Factory", "factory", 2123.0889,-2267.8140,30.1226, 2048.2009,-2199.2488,30.1229);
		AddMap("Italy", "italy", 666.7128,-2317.1716,107.6724,  723.1223,-2314.8726,107.0072);
		AddMap("Metro", "trainstop", 1941.0496,-2995.8530,11.3687, 2039.7579,-2895.3823,11.3687);
		AddMap("Forest", "forest", 3664.81,-383.98,16.34, 3678.7402,-703.1265,11.9859);

        maptimer = SetTimer("Second",1000,true);
        DisableInteriorEnterExits();
        UsePlayerPedAnims();
        SetGameModeText("Battlefield™");
        SendRconCommand("mapname Container");
        SendRconCommand("loadfs container");
		LastFS = "container";

		SetTimer("SendMSG", 180000, true);

        AddPlayerClass(111,-1132.0251,1041.6741,1345.7401,271.0460,0,0,0,0,0,0); // RUS
		AddPlayerClass(287,-973.3190,1077.4526,1344.9951,90.9010,0,0,0,0,0,0); // USA

  		russkor = TextDrawCreate(286.000000, 413.625000, "rusbox");
		TextDrawLetterSize(russkor, 0.000000, 3.823610);
		TextDrawTextSize(russkor, 197.000000, 0.000000);
		TextDrawAlignment(russkor, 1);
		TextDrawColor(russkor, 0);
		TextDrawUseBox(russkor, true);
		TextDrawBoxColor(russkor, -1275068196);
		TextDrawSetShadow(russkor, 0);
		TextDrawSetOutline(russkor, 0);
		TextDrawBackgroundColor(russkor, -939523866);
		TextDrawFont(russkor, 0);

		usaskor = TextDrawCreate(430.500000, 413.625000, "usabox");
		TextDrawLetterSize(usaskor, 0.000000, 3.672222);
		TextDrawTextSize(usaskor, 341.500000, 0.000000);
		TextDrawAlignment(usaskor, 1);
		TextDrawColor(usaskor, 0);
		TextDrawUseBox(usaskor, true);
		TextDrawBoxColor(usaskor, 422957567);
		TextDrawSetShadow(usaskor, 0);
		TextDrawSetOutline(usaskor, 0);
		TextDrawBackgroundColor(usaskor, 726131711);
		TextDrawFont(usaskor, 0);

		crnapozadina = TextDrawCreate(645.500000, 413.625000, "crnapozadina");
		TextDrawLetterSize(crnapozadina, 0.000000, 3.620833);
		TextDrawTextSize(crnapozadina, -2.000000, 0.000000);
		TextDrawAlignment(crnapozadina, 1);
		TextDrawColor(crnapozadina, 25494085);
		TextDrawUseBox(crnapozadina, true);
		TextDrawBoxColor(crnapozadina, 102);
		TextDrawSetShadow(crnapozadina, 0);
		TextDrawSetOutline(crnapozadina, 0);
		TextDrawBackgroundColor(crnapozadina, 90);
		TextDrawFont(crnapozadina, 0);


		TDtKills = TextDrawCreate(205.500000, 422.625000, "RUS: 00");
		TextDrawLetterSize(TDtKills, 0.449999, 1.600000);
		TextDrawAlignment(TDtKills, 1);
		TextDrawColor(TDtKills, -1);
		TextDrawSetShadow(TDtKills, 0);
		TextDrawSetOutline(TDtKills, 1);
		TextDrawBackgroundColor(TDtKills, 51);
		TextDrawFont(TDtKills, 2);
		TextDrawSetProportional(TDtKills, 1);

		TDctKills = TextDrawCreate(349.000000, 422.187500, "USA: 00");
		TextDrawLetterSize(TDctKills, 0.449999, 1.600000);
		TextDrawAlignment(TDctKills, 1);
		TextDrawColor(TDctKills, -1);
		TextDrawSetShadow(TDctKills, 0);
		TextDrawSetOutline(TDctKills, 1);
		TextDrawBackgroundColor(TDctKills, 51);
		TextDrawFont(TDctKills, 2);
		TextDrawSetProportional(TDctKills, 1);

		Timeleft = TextDrawCreate(287.000000, 422.625000, "00:00");
		TextDrawLetterSize(Timeleft, 0.449999, 1.600000);
		TextDrawAlignment(Timeleft, 1);
		TextDrawColor(Timeleft, -1);
		TextDrawSetShadow(Timeleft, 0);
		TextDrawSetOutline(Timeleft, 1);
		TextDrawBackgroundColor(Timeleft, 51);
		TextDrawFont(Timeleft, 2);
		TextDrawSetProportional(Timeleft, 1);

		CurrentMap = TextDrawCreate(434.000000, 430.937500, "Container");
		TextDrawLetterSize(CurrentMap, 0.449999, 1.600000);
		TextDrawAlignment(CurrentMap, 1);
		TextDrawColor(CurrentMap, 1304553727);
		TextDrawSetOutline(CurrentMap, 1);
		TextDrawBackgroundColor(CurrentMap, 65535);
		TextDrawSetShadow(CurrentMap, 0);
		TextDrawSetOutline(CurrentMap, 1);
		TextDrawBackgroundColor(CurrentMap, 51);
		TextDrawFont(CurrentMap, 1);
		TextDrawSetProportional(CurrentMap, 1);

		WinnerBackground1=TextDrawCreate(459.000000, 95.000000, "_");
		TextDrawBackgroundColor(WinnerBackground1, 255);
		TextDrawFont(WinnerBackground1, 1);
		TextDrawLetterSize(WinnerBackground1, 1.100000, 11.299990);
		TextDrawColor(WinnerBackground1, -1);
		TextDrawSetOutline(WinnerBackground1, 0);
		TextDrawSetProportional(WinnerBackground1, 1);
		TextDrawSetShadow(WinnerBackground1, 1);
		TextDrawUseBox(WinnerBackground1, 1);
		TextDrawBoxColor(WinnerBackground1, -16776961);
		TextDrawTextSize(WinnerBackground1, 163.000000, 63.000000);
		TextDrawSetSelectable(WinnerBackground1, 0);

        WinnerBackground2=TextDrawCreate(460.000000, 171.000000, "_");
		TextDrawBackgroundColor(WinnerBackground2, 255);
		TextDrawFont(WinnerBackground2, 1);
		TextDrawLetterSize(WinnerBackground2, 0.500000, 3.000000);
		TextDrawColor(WinnerBackground2, -1);
		TextDrawSetOutline(WinnerBackground2, 0);
		TextDrawSetProportional(WinnerBackground2, 1);
		TextDrawSetShadow(WinnerBackground2, 1);
		TextDrawUseBox(WinnerBackground2, 1);
		TextDrawBoxColor(WinnerBackground2, 255);
		TextDrawTextSize(WinnerBackground2, 163.000000, 30.000000);
		TextDrawSetSelectable(WinnerBackground2, 0);

		Winningteam=TextDrawCreate(178.000000, 114.000000, "United States has won the round");
		TextDrawBackgroundColor(Winningteam, 255);
		TextDrawFont(Winningteam, 2);
		TextDrawLetterSize(Winningteam, 0.310000, 3.799999);
		TextDrawColor(Winningteam, -1);
		TextDrawSetOutline(Winningteam, 0);
		TextDrawSetProportional(Winningteam, 1);
		TextDrawSetShadow(Winningteam, 1);
		TextDrawSetSelectable(Winningteam, 0);

		MVP=TextDrawCreate(170.000000, 177.000000, "MVP: AmalMxD91111111111111111 (ID) with 1000 kills!");
		TextDrawBackgroundColor(MVP, 255);
		TextDrawFont(MVP, 2);
		TextDrawLetterSize(MVP, 0.250000, 1.499999);
		TextDrawColor(MVP, -1);
		TextDrawSetOutline(MVP, 0);
		TextDrawSetProportional(MVP, 1);
		TextDrawSetShadow(MVP, 1);
		TextDrawSetSelectable(MVP, 0);
		//what is text draw10? - already define on top of script

		Textdraw0 = TextDrawCreate(510.000000, 282.375000, "_");
		TextDrawLetterSize(Textdraw0, 0.000000, 11.009720);
		TextDrawTextSize(Textdraw0, 137.000000, 0.000000);
		TextDrawAlignment(Textdraw0, 1);
		TextDrawColor(Textdraw0, 0);
		TextDrawUseBox(Textdraw0, true);
		TextDrawBoxColor(Textdraw0, 150);
		TextDrawSetShadow(Textdraw0, 0);
		TextDrawSetOutline(Textdraw0, 0);
		TextDrawBackgroundColor(Textdraw0, 100);
		TextDrawFont(Textdraw0, 0);

		Textdraw1 = TextDrawCreate(275.000000, 282.187500, "Pick a class");
		TextDrawLetterSize(Textdraw1, 0.449999, 1.600000);
		TextDrawAlignment(Textdraw1, 1);
		TextDrawColor(Textdraw1, -1);
		TextDrawSetShadow(Textdraw1, 0);
		TextDrawSetOutline(Textdraw1, 1);
		TextDrawBackgroundColor(Textdraw1, 255);
		TextDrawFont(Textdraw1, 1);
		TextDrawSetProportional(Textdraw1, 1);

		Textdraw2 = TextDrawCreate(261.000000, 301.187500, "_");//1
		TextDrawLetterSize(Textdraw2, 0.000000, 8.418054);
		TextDrawTextSize(Textdraw2, 145.000000, 0.000000);
		TextDrawAlignment(Textdraw2, 1);
		TextDrawColor(Textdraw2, 0);
		TextDrawUseBox(Textdraw2, true);
		TextDrawBoxColor(Textdraw2, 1179010444);
		TextDrawSetShadow(Textdraw2, 0);
		TextDrawSetOutline(Textdraw2, 0);
		TextDrawFont(Textdraw2, 0);

		Textdraw3 = TextDrawCreate(380.500000, 301.187500, "_");//2
		TextDrawLetterSize(Textdraw3, 0.000000, 8.430555);
		TextDrawTextSize(Textdraw3, 263.000000, 0.000000);
		TextDrawAlignment(Textdraw3, 1);
		TextDrawColor(Textdraw3, 0);
		TextDrawUseBox(Textdraw3, true);
		TextDrawBoxColor(Textdraw3, 1179010444);
		TextDrawSetShadow(Textdraw3, 0);
		TextDrawSetOutline(Textdraw3, 0);
		TextDrawFont(Textdraw3, 0);

		Textdraw4 = TextDrawCreate(501.000000, 301.187500, "_");//3
		TextDrawLetterSize(Textdraw4, 0.000000, 8.431942);
		TextDrawTextSize(Textdraw4, 383.500000, 0.000000);
		TextDrawAlignment(Textdraw4, 1);
		TextDrawColor(Textdraw4, 0);
		TextDrawUseBox(Textdraw4, true);
		TextDrawBoxColor(Textdraw4, 1179010444);
		TextDrawSetShadow(Textdraw4, 0);
		TextDrawSetOutline(Textdraw4, 0);
		TextDrawFont(Textdraw4, 0);

		Textdraw5 = TextDrawCreate(166.500000, 332.500000, "Support");//1
		TextDrawLetterSize(Textdraw5, 0.517997, 1.571249);
		TextDrawAlignment(Textdraw5, 1);
		TextDrawColor(Textdraw5, -1);
		TextDrawSetShadow(Textdraw5, 0);
		TextDrawSetOutline(Textdraw5, 2);
		TextDrawBackgroundColor(Textdraw5, 51);
		TextDrawFont(Textdraw5, 1);
		TextDrawSetProportional(Textdraw5, 1);

		Textdraw6 = TextDrawCreate(291.000000, 332.937500, "Assault");//2
		TextDrawLetterSize(Textdraw6, 0.449999, 1.600000);
		TextDrawAlignment(Textdraw6, 1);
		TextDrawColor(Textdraw6, -1);
		TextDrawSetShadow(Textdraw6, 0);
		TextDrawSetOutline(Textdraw6, 2);
		TextDrawBackgroundColor(Textdraw6, 51);
		TextDrawFont(Textdraw6, 1);
		TextDrawSetProportional(Textdraw6, 1);

		Textdraw7 = TextDrawCreate(417.500000, 332.937500, "Recon");//3
		TextDrawLetterSize(Textdraw7, 0.449999, 1.600000);
		TextDrawAlignment(Textdraw7, 1);
		TextDrawColor(Textdraw7, -1);
		TextDrawSetShadow(Textdraw7, 0);
		TextDrawSetOutline(Textdraw7, 2);
		TextDrawBackgroundColor(Textdraw7, 51);
		TextDrawFont(Textdraw7, 1);
		TextDrawSetProportional(Textdraw7, 1);

		Textdraw8 = TextDrawCreate(262.500000, 301.625000, "_");//1
		TextDrawLetterSize(Textdraw8, 0.000000, 8.481945);
		TextDrawTextSize(Textdraw8, 143.500000, 0.000000);
		TextDrawAlignment(Textdraw8, 1);
		TextDrawColor(Textdraw8, 0);
		TextDrawUseBox(Textdraw8, true);
		TextDrawBoxColor(Textdraw8, 0);
		TextDrawSetShadow(Textdraw8, 0);
		TextDrawSetOutline(Textdraw8, 0);
		TextDrawFont(Textdraw8, 0);

		Textdraw9 = TextDrawCreate(381.500000, 300.750000, "_");//2
		TextDrawLetterSize(Textdraw9, 0.000000, 4.280556);
		TextDrawTextSize(Textdraw9, 263.500000, 0.000000);
		TextDrawAlignment(Textdraw9, 1);
		TextDrawColor(Textdraw9, 0);
		TextDrawUseBox(Textdraw9, true);
		TextDrawBoxColor(Textdraw9, 0);
		TextDrawSetShadow(Textdraw9, 0);
		TextDrawSetOutline(Textdraw9, 0);
		TextDrawFont(Textdraw9, 0);

		Textdraw10 = TextDrawCreate(502.000000, 301.625000, "_");//3
		TextDrawLetterSize(Textdraw10, 0.000000, 8.481945);
		TextDrawTextSize(Textdraw10, 384.000000, 0.000000);
		TextDrawAlignment(Textdraw10, 1);
		TextDrawColor(Textdraw10, 0);
		TextDrawUseBox(Textdraw10, true);
		TextDrawBoxColor(Textdraw10, 0);
		TextDrawSetShadow(Textdraw10, 0);
		TextDrawSetOutline(Textdraw10, 0);
		TextDrawBackgroundColor(Textdraw10, -256);
		TextDrawFont(Textdraw10, 0);

		//ASSIST TEXTDRAW

		assisttext = TextDrawCreate(255 ,71 , "Assist: 50 EXP!");
		TextDrawFont(assisttext , 1);
		TextDrawLetterSize(assisttext , 0.6, 4.2);
		TextDrawColor(assisttext , 0x0088ffFF);
		TextDrawSetOutline(assisttext , false);
		TextDrawSetProportional(assisttext , true);
		TextDrawSetShadow(assisttext , 1);

		// KILL TEXTDRAW
		killtext = TextDrawCreate(274 ,116 , "Kill: 100 XP");
		TextDrawFont(killtext , 1);
		TextDrawLetterSize(killtext , 0.6, 4.2);
		TextDrawColor(killtext , 0x4050ffFF);
		TextDrawSetOutline(killtext , false);
		TextDrawSetProportional(killtext , true);
		TextDrawSetShadow(killtext , 1);

		// HEADSHOT TEXTDRAW
		headshottext = TextDrawCreate(274 ,116 , "Headshot: 130 XP");
		TextDrawFont(headshottext , 1);
		TextDrawLetterSize(headshottext , 0.6, 4.2);
		TextDrawColor(headshottext , 0x4050ffFF);
		TextDrawSetOutline(headshottext , false);
		TextDrawSetProportional(headshottext , true);
		TextDrawSetShadow(headshottext , 1);


		// WELCOME TEXT
		TDEditor_TD[0] = TextDrawCreate(189.000000, 117.375000, "Welcome_to_Battlefield!");
		TextDrawLetterSize(TDEditor_TD[0], 0.505499, 2.694374);
		TextDrawAlignment(TDEditor_TD[0], 1);
		TextDrawColor(TDEditor_TD[0], 1304553727);
		TextDrawSetShadow(TDEditor_TD[0], 1);
		TextDrawSetOutline(TDEditor_TD[0], 1);
		TextDrawBackgroundColor(TDEditor_TD[0], 65535);
		TextDrawFont(TDEditor_TD[0], 2);
		TextDrawSetProportional(TDEditor_TD[0], 1);
		TextDrawSetShadow(TDEditor_TD[0], 1);

		//AK47
		TDEditor_TD[1] = TextDrawCreate(444.500000, 95.062500, "");
		TextDrawLetterSize(TDEditor_TD[1], 0.000000, 0.000000);
		TextDrawTextSize(TDEditor_TD[1], 77.000000, 75.000000);
		TextDrawAlignment(TDEditor_TD[1], 1);
		TextDrawColor(TDEditor_TD[1], -1);
		TextDrawSetShadow(TDEditor_TD[1], 0);
		TextDrawSetOutline(TDEditor_TD[1], 0);
		TextDrawBackgroundColor(TDEditor_TD[1], 0);
		TextDrawFont(TDEditor_TD[1], 5);
		TextDrawSetProportional(TDEditor_TD[1], 0);
		TextDrawSetShadow(TDEditor_TD[1], 0);
		TextDrawSetPreviewModel(TDEditor_TD[1], 355);
		TextDrawSetPreviewRot(TDEditor_TD[1], 0.000000, -40.000000, 0.000000, 2.000000);

		//Shotgun
		TDEditor_TD[2] = TextDrawCreate(139.000000, 89.375000, "");
		TextDrawLetterSize(TDEditor_TD[2], 0.000000, 0.000000);
		TextDrawTextSize(TDEditor_TD[2], 90.000000, 90.000000);
		TextDrawAlignment(TDEditor_TD[2], 1);
		TextDrawColor(TDEditor_TD[2], -1);
		TextDrawSetShadow(TDEditor_TD[2], 0);
		TextDrawSetOutline(TDEditor_TD[2], 0);
		TextDrawBackgroundColor(TDEditor_TD[2], 0);
		TextDrawFont(TDEditor_TD[2], 5);
		TextDrawSetProportional(TDEditor_TD[2], 0);
		TextDrawSetShadow(TDEditor_TD[2], 0);
		TextDrawSetPreviewModel(TDEditor_TD[2], 349);
		TextDrawSetPreviewRot(TDEditor_TD[2], 180.000000, 150.000000, 0.000000, 2.200000);

		//CRNA POZADINA
		TDEditor_TD[3] = TextDrawCreate(145.000000, 95.937500, "box");
		TextDrawLetterSize(TDEditor_TD[3], 0.000000, 6.050000);
		TextDrawTextSize(TDEditor_TD[3], 529.000000, 0.000000);
		TextDrawAlignment(TDEditor_TD[3], 1);
		TextDrawColor(TDEditor_TD[3], -1);
		TextDrawUseBox(TDEditor_TD[3], 1);
		TextDrawBoxColor(TDEditor_TD[3], 336);
		TextDrawSetShadow(TDEditor_TD[3], 0);
		TextDrawSetOutline(TDEditor_TD[3], 0);
		TextDrawBackgroundColor(TDEditor_TD[3], 255);
		TextDrawFont(TDEditor_TD[3], 1);
		TextDrawSetProportional(TDEditor_TD[3], 1);
		TextDrawSetShadow(TDEditor_TD[3], 0);
		
		//**************************************//
		
		Selection_TD[0] = TextDrawCreate(70.500000, 29.437500, "box");
		TextDrawLetterSize(Selection_TD[0], 0.000000, 37.600002);
		TextDrawTextSize(Selection_TD[0], 571.000000, 0.000000);
		TextDrawAlignment(Selection_TD[0], 1);
		TextDrawColor(Selection_TD[0], -1);
		TextDrawUseBox(Selection_TD[0], 1);
		TextDrawBoxColor(Selection_TD[0], 125);
		TextDrawSetShadow(Selection_TD[0], 0);
		TextDrawSetOutline(Selection_TD[0], 0);
		TextDrawBackgroundColor(Selection_TD[0], 255);
		TextDrawFont(Selection_TD[0], 1);
		TextDrawSetProportional(Selection_TD[0], 1);
		TextDrawSetShadow(Selection_TD[0], 0);

		Selection_TD[1] = TextDrawCreate(262.500000, 53.500000, "Choose_your_side");
		TextDrawLetterSize(Selection_TD[1], 0.406000, 1.753124);
		TextDrawAlignment(Selection_TD[1], 1);
		TextDrawColor(Selection_TD[1], -1);
		TextDrawSetShadow(Selection_TD[1], 108);
		TextDrawSetOutline(Selection_TD[1], -1);
		TextDrawBackgroundColor(Selection_TD[1], 255);
		TextDrawFont(Selection_TD[1], 1);
		TextDrawSetProportional(Selection_TD[1], 1);
		TextDrawSetShadow(Selection_TD[1], 108);

		Selection_TD[2] = TextDrawCreate(100.500000, 137.062500, "plava");
		TextDrawLetterSize(Selection_TD[2], 0.000000, 2.549999);
		TextDrawTextSize(Selection_TD[2], 238.000000, 0.000000);
		TextDrawAlignment(Selection_TD[2], 1);
		TextDrawColor(Selection_TD[2], 65535);
		TextDrawUseBox(Selection_TD[2], 1);
		TextDrawBoxColor(Selection_TD[2], 41215);
		TextDrawSetShadow(Selection_TD[2], 0);
		TextDrawSetOutline(Selection_TD[2], 0);
		TextDrawBackgroundColor(Selection_TD[2], 65535);
		TextDrawFont(Selection_TD[2], 1);
		TextDrawSetProportional(Selection_TD[2], 1);
		TextDrawSetShadow(Selection_TD[2], 0);

		Selection_TD[3] = TextDrawCreate(100.500000, 109.500000, "bela");
		TextDrawLetterSize(Selection_TD[3], 0.000000, 2.655008);
		TextDrawTextSize(Selection_TD[3], 238.000000, 0.000000);
		TextDrawAlignment(Selection_TD[3], 1);
		TextDrawColor(Selection_TD[3], -1);
		TextDrawUseBox(Selection_TD[3], 1);
		TextDrawBoxColor(Selection_TD[3], -1);
		TextDrawSetShadow(Selection_TD[3], 0);
		TextDrawSetOutline(Selection_TD[3], 0);
		TextDrawBackgroundColor(Selection_TD[3], 255);
		TextDrawFont(Selection_TD[3], 1);
		TextDrawSetProportional(Selection_TD[3], 1);
		TextDrawSetShadow(Selection_TD[3], 0);

		Selection_TD[4] = TextDrawCreate(101.000000, 162.437500, "crvena");
		TextDrawLetterSize(Selection_TD[4], 0.000000, 2.232992);
		TextDrawTextSize(Selection_TD[4], 238.380340, 0.000000);
		TextDrawAlignment(Selection_TD[4], 1);
		TextDrawColor(Selection_TD[4], -1);
		TextDrawUseBox(Selection_TD[4], 1);
		TextDrawBoxColor(Selection_TD[4], -16776961);
		TextDrawSetShadow(Selection_TD[4], 0);
		TextDrawSetOutline(Selection_TD[4], 0);
		TextDrawBackgroundColor(Selection_TD[4], 255);
		TextDrawFont(Selection_TD[4], 1);
		TextDrawSetProportional(Selection_TD[4], 1);
		TextDrawSetShadow(Selection_TD[4], 0);

		Selection_TD[5] = TextDrawCreate(242.500000, 134.000000, "box");
		TextDrawLetterSize(Selection_TD[5], 0.000000, 3.200001);
		TextDrawTextSize(Selection_TD[5], 531.000000, 0.000000);
		TextDrawAlignment(Selection_TD[5], 1);
		TextDrawColor(Selection_TD[5], -1);
		TextDrawUseBox(Selection_TD[5], 1);
		TextDrawBoxColor(Selection_TD[5], -1728053118);
		TextDrawSetShadow(Selection_TD[5], 0);
		TextDrawSetOutline(Selection_TD[5], 0);
		TextDrawBackgroundColor(Selection_TD[5], -2147483393);
		TextDrawFont(Selection_TD[5], 1);
		TextDrawSetProportional(Selection_TD[5], 1);
		TextDrawSetShadow(Selection_TD[5], 0);

		Selection_TD[6] = TextDrawCreate(343.500000, 140.562500, "Russian_Forces");
		TextDrawLetterSize(Selection_TD[6], 0.400000, 1.600000);
		TextDrawAlignment(Selection_TD[6], 1);
		TextDrawColor(Selection_TD[6], -16776961);
		TextDrawSetShadow(Selection_TD[6], 0);
		TextDrawSetOutline(Selection_TD[6], -1);
		TextDrawBackgroundColor(Selection_TD[6], -2147483393);
		TextDrawFont(Selection_TD[6], 1);
		TextDrawSetProportional(Selection_TD[6], 1);
		TextDrawSetShadow(Selection_TD[6], 0);

		Selection_TD[7] = TextDrawCreate(398.899993, 269.187500, "amerikabelo");
		TextDrawLetterSize(Selection_TD[7], 0.000000, 8.149999);
		TextDrawTextSize(Selection_TD[7], 546.000000, 0.000000);
		TextDrawAlignment(Selection_TD[7], 1);
		TextDrawColor(Selection_TD[7], 13107280);
		TextDrawUseBox(Selection_TD[7], 1);
		TextDrawBoxColor(Selection_TD[7], -1);
		TextDrawSetShadow(Selection_TD[7], 0);
		TextDrawSetOutline(Selection_TD[7], 0);
		TextDrawBackgroundColor(Selection_TD[7], 255);
		TextDrawFont(Selection_TD[7], 1);
		TextDrawSetProportional(Selection_TD[7], 1);
		TextDrawSetShadow(Selection_TD[7], 0);

		Selection_TD[8] = TextDrawCreate(399.000000, 269.187500, "amerikaplavo");
		TextDrawLetterSize(Selection_TD[8], 0.000000, 4.099999);
		TextDrawTextSize(Selection_TD[8], 460.000000, 0.000000);
		TextDrawAlignment(Selection_TD[8], 1);
		TextDrawColor(Selection_TD[8], -1);
		TextDrawUseBox(Selection_TD[8], 1);
		TextDrawBoxColor(Selection_TD[8], 41215);
		TextDrawSetShadow(Selection_TD[8], 0);
		TextDrawSetOutline(Selection_TD[8], 0);
		TextDrawBackgroundColor(Selection_TD[8], 255);
		TextDrawFont(Selection_TD[8], 1);
		TextDrawSetProportional(Selection_TD[8], 1);
		TextDrawSetShadow(Selection_TD[8], 0);

		Selection_TD[9] = TextDrawCreate(399.000000, 337.000000, "crvena5");
		TextDrawLetterSize(Selection_TD[9], 0.000000, 0.600001);
		TextDrawTextSize(Selection_TD[9], 546.050048, 0.000000);
		TextDrawAlignment(Selection_TD[9], 1);
		TextDrawColor(Selection_TD[9], -1);
		TextDrawUseBox(Selection_TD[9], 1);
		TextDrawBoxColor(Selection_TD[9], -1523963137);
		TextDrawSetShadow(Selection_TD[9], 0);
		TextDrawSetOutline(Selection_TD[9], 0);
		TextDrawBackgroundColor(Selection_TD[9], 255);
		TextDrawFont(Selection_TD[9], 1);
		TextDrawSetProportional(Selection_TD[9], 1);
		TextDrawSetShadow(Selection_TD[9], 0);

		Selection_TD[10] = TextDrawCreate(399.000000, 319.062500, "crvena4");
		TextDrawLetterSize(Selection_TD[10], 0.000000, 0.600001);
		TextDrawTextSize(Selection_TD[10], 546.000000, 0.000000);
		TextDrawAlignment(Selection_TD[10], 1);
		TextDrawColor(Selection_TD[10], -1);
		TextDrawUseBox(Selection_TD[10], 1);
		TextDrawBoxColor(Selection_TD[10], -1523963137);
		TextDrawSetShadow(Selection_TD[10], 0);
		TextDrawSetOutline(Selection_TD[10], 0);
		TextDrawBackgroundColor(Selection_TD[10], 255);
		TextDrawFont(Selection_TD[10], 1);
		TextDrawSetProportional(Selection_TD[10], 1);
		TextDrawSetShadow(Selection_TD[10], 0);

		Selection_TD[11] = TextDrawCreate(464.000000, 300.550018, "crvena3");
		TextDrawLetterSize(Selection_TD[11], 0.000000, 0.600000);
		TextDrawTextSize(Selection_TD[11], 546.000000, 0.000000);
		TextDrawAlignment(Selection_TD[11], 1);
		TextDrawColor(Selection_TD[11], -1);
		TextDrawUseBox(Selection_TD[11], 1);
		TextDrawBoxColor(Selection_TD[11], -1523963137);
		TextDrawSetShadow(Selection_TD[11], 0);
		TextDrawSetOutline(Selection_TD[11], 0);
		TextDrawBackgroundColor(Selection_TD[11], 255);
		TextDrawFont(Selection_TD[11], 1);
		TextDrawSetProportional(Selection_TD[11], 1);
		TextDrawSetShadow(Selection_TD[11], 0);

		Selection_TD[12] = TextDrawCreate(464.000000, 282.612518, "crvena2");
		TextDrawLetterSize(Selection_TD[12], 0.000000, 0.600000);
		TextDrawTextSize(Selection_TD[12], 545.989013, 0.000000);
		TextDrawAlignment(Selection_TD[12], 1);
		TextDrawColor(Selection_TD[12], -1);
		TextDrawUseBox(Selection_TD[12], 1);
		TextDrawBoxColor(Selection_TD[12], -1523963137);
		TextDrawSetShadow(Selection_TD[12], 0);
		TextDrawSetOutline(Selection_TD[12], 0);
		TextDrawBackgroundColor(Selection_TD[12], 255);
		TextDrawFont(Selection_TD[12], 1);
		TextDrawSetProportional(Selection_TD[12], 1);
		TextDrawSetShadow(Selection_TD[12], 0);

		Selection_TD[13] = TextDrawCreate(463.500000, 269.487518, "crvena1");
		TextDrawLetterSize(Selection_TD[13], 0.000000, 0.100000);
		TextDrawTextSize(Selection_TD[13], 546.030029, 0.000000);
		TextDrawAlignment(Selection_TD[13], 1);
		TextDrawColor(Selection_TD[13], -1);
		TextDrawUseBox(Selection_TD[13], 1);
		TextDrawBoxColor(Selection_TD[13], -1523963137);
		TextDrawSetShadow(Selection_TD[13], 0);
		TextDrawSetOutline(Selection_TD[13], 0);
		TextDrawBackgroundColor(Selection_TD[13], 255);
		TextDrawFont(Selection_TD[13], 1);
		TextDrawSetProportional(Selection_TD[13], 1);
		TextDrawSetShadow(Selection_TD[13], 0);

		Selection_TD[14] = TextDrawCreate(396.000000, 269.625000, "[][][][][][]");
		TextDrawLetterSize(Selection_TD[14], 0.168500, 0.703124);
		TextDrawAlignment(Selection_TD[14], 1);
		TextDrawColor(Selection_TD[14], -1);
		TextDrawSetShadow(Selection_TD[14], 0);
		TextDrawSetOutline(Selection_TD[14], 0);
		TextDrawBackgroundColor(Selection_TD[14], 255);
		TextDrawFont(Selection_TD[14], 2);
		TextDrawSetProportional(Selection_TD[14], 1);
		TextDrawSetShadow(Selection_TD[14], 0);

		Selection_TD[15] = TextDrawCreate(396.000000, 280.125000, "[][][][][][]");
		TextDrawLetterSize(Selection_TD[15], 0.168500, 0.703124);
		TextDrawAlignment(Selection_TD[15], 1);
		TextDrawColor(Selection_TD[15], -1);
		TextDrawSetShadow(Selection_TD[15], 0);
		TextDrawSetOutline(Selection_TD[15], 0);
		TextDrawBackgroundColor(Selection_TD[15], 255);
		TextDrawFont(Selection_TD[15], 2);
		TextDrawSetProportional(Selection_TD[15], 1);
		TextDrawSetShadow(Selection_TD[15], 0);

		Selection_TD[16] = TextDrawCreate(396.000000, 289.750000, "[][][][][][]");
		TextDrawLetterSize(Selection_TD[16], 0.168500, 0.703124);
		TextDrawAlignment(Selection_TD[16], 1);
		TextDrawColor(Selection_TD[16], -1);
		TextDrawSetShadow(Selection_TD[16], 0);
		TextDrawSetOutline(Selection_TD[16], 0);
		TextDrawBackgroundColor(Selection_TD[16], 255);
		TextDrawFont(Selection_TD[16], 2);
		TextDrawSetProportional(Selection_TD[16], 1);
		TextDrawSetShadow(Selection_TD[16], 0);

		Selection_TD[17] = TextDrawCreate(396.299957, 299.375000, "[][][][][][]");
		TextDrawLetterSize(Selection_TD[17], 0.168500, 0.703124);
		TextDrawAlignment(Selection_TD[17], 1);
		TextDrawColor(Selection_TD[17], -1);
		TextDrawSetShadow(Selection_TD[17], 0);
		TextDrawSetOutline(Selection_TD[17], 0);
		TextDrawBackgroundColor(Selection_TD[17], 255);
		TextDrawFont(Selection_TD[17], 2);
		TextDrawSetProportional(Selection_TD[17], 1);
		TextDrawSetShadow(Selection_TD[17], 0);

		Selection_TD[18] = TextDrawCreate(95.900016, 294.125000, "amerikatext");
		TextDrawLetterSize(Selection_TD[18], 0.000000, 3.399999);
		TextDrawTextSize(Selection_TD[18], 394.030029, 0.000000);
		TextDrawAlignment(Selection_TD[18], 1);
		TextDrawColor(Selection_TD[18], -1);
		TextDrawUseBox(Selection_TD[18], 1);
		TextDrawBoxColor(Selection_TD[18], 58960);
		TextDrawSetShadow(Selection_TD[18], 0);
		TextDrawSetOutline(Selection_TD[18], 0);
		TextDrawBackgroundColor(Selection_TD[18], 255);
		TextDrawFont(Selection_TD[18], 1);
		TextDrawSetProportional(Selection_TD[18], 1);
		TextDrawSetShadow(Selection_TD[18], 0);

		Selection_TD[19] = TextDrawCreate(205.000000, 301.525024, "United_States");
		TextDrawLetterSize(Selection_TD[19], 0.400000, 1.600000);
		TextDrawAlignment(Selection_TD[19], 1);
		TextDrawColor(Selection_TD[19], 16777215);
		TextDrawSetShadow(Selection_TD[19], 0);
		TextDrawSetOutline(Selection_TD[19], -1);
		TextDrawBackgroundColor(Selection_TD[19], 41215);
		TextDrawFont(Selection_TD[19], 1);
		TextDrawSetProportional(Selection_TD[19], 1);
		TextDrawSetShadow(Selection_TD[19], 0);

		Selection_TD[20] = TextDrawCreate(100.500000, 109.062500, "selectrus");
		TextDrawLetterSize(Selection_TD[20], 0.000000, 8.249999);
		TextDrawTextSize(Selection_TD[20], 239.000000, 100.000000);//239,50
		TextDrawAlignment(Selection_TD[20], 1);
		TextDrawColor(Selection_TD[20], -1);
		TextDrawUseBox(Selection_TD[20], 1);
		TextDrawBoxColor(Selection_TD[20], 1);
		TextDrawSetShadow(Selection_TD[20], 0);
		TextDrawSetOutline(Selection_TD[20], 0);
		TextDrawBackgroundColor(Selection_TD[20], 255);
		TextDrawFont(Selection_TD[20], 1);
		TextDrawSetProportional(Selection_TD[20], 1);
		TextDrawSetShadow(Selection_TD[20], 0);
		

		Selection_TD[21] = TextDrawCreate(242.500000, 133.562500, "selectrus2");
		TextDrawLetterSize(Selection_TD[21], 0.000000, 3.649999);
		TextDrawTextSize(Selection_TD[21], 532.000000, 75.000000);//532,50
		TextDrawAlignment(Selection_TD[21], 1);
		TextDrawColor(Selection_TD[21], -1);
		TextDrawUseBox(Selection_TD[21], 1);
		TextDrawBoxColor(Selection_TD[21], 1);
		TextDrawSetShadow(Selection_TD[21], 0);
		TextDrawSetOutline(Selection_TD[21], 0);
		TextDrawBackgroundColor(Selection_TD[21], 255);
		TextDrawFont(Selection_TD[21], 1);
		TextDrawSetProportional(Selection_TD[21], 1);
		TextDrawSetShadow(Selection_TD[21], 0);
		

		Selection_TD[22] = TextDrawCreate(399.000000, 269.187500, "selectusa");
		TextDrawLetterSize(Selection_TD[22], 0.000000, 8.149999);
		TextDrawTextSize(Selection_TD[22], 547.000000, 75.000000);//547,50
		TextDrawAlignment(Selection_TD[22], 1);
		TextDrawColor(Selection_TD[22], -1);//-1
		TextDrawUseBox(Selection_TD[22], 1);
		TextDrawBoxColor(Selection_TD[22],1);//1
		TextDrawSetShadow(Selection_TD[22], 0);
		TextDrawSetOutline(Selection_TD[22], 0);
		TextDrawBackgroundColor(Selection_TD[22], 255);
		TextDrawFont(Selection_TD[22], 1);
		TextDrawSetProportional(Selection_TD[22], 1);
		TextDrawSetShadow(Selection_TD[22], 0);
		

		Selection_TD[23] = TextDrawCreate(96.000000, 294.125000, "selectusa2");
		TextDrawLetterSize(Selection_TD[23], 0.000000, 3.549999);
		TextDrawTextSize(Selection_TD[23], 395.000000, 75.000000);//395,50
		TextDrawAlignment(Selection_TD[23], 1);
		TextDrawColor(Selection_TD[23], -1);
		TextDrawUseBox(Selection_TD[23], 1);
		TextDrawBoxColor(Selection_TD[23], 1);
		TextDrawSetShadow(Selection_TD[23], 0);
		TextDrawSetOutline(Selection_TD[23], 0);
		TextDrawBackgroundColor(Selection_TD[23], 255);
		TextDrawFont(Selection_TD[23], 1);
		TextDrawSetProportional(Selection_TD[23], 1);
		TextDrawSetShadow(Selection_TD[23], 0);

		
		
		//**************************************//
  		WasteDeAMXersTime();
    	AntiDeAMX();
    	

		//
		TextDrawSetSelectable(Textdraw0,false);
		TextDrawSetSelectable(Textdraw1,false);
		TextDrawSetSelectable(Textdraw2,true);
		TextDrawSetSelectable(Textdraw2,true);
		TextDrawSetSelectable(Textdraw3,true);
		TextDrawSetSelectable(Textdraw4,true);
		TextDrawSetSelectable(Textdraw5,true);
		TextDrawSetSelectable(Textdraw6,true);
		TextDrawSetSelectable(Textdraw7,true);
		TextDrawSetSelectable(Textdraw8,true);
		TextDrawSetSelectable(Textdraw9,true);
		TextDrawSetSelectable(Textdraw10,true);
		TextDrawSetSelectable(Selection_TD[20], true);
		TextDrawSetSelectable(Selection_TD[21], true);
		TextDrawSetSelectable(Selection_TD[22], true);
		TextDrawSetSelectable(Selection_TD[23], true);


        ShowPlayerMarkers(PLAYER_MARKERS_MODE_OFF);

		for(new i=0;i<MAX_PLAYERS;i++)
		{
		    PlayerHealth[i]=TextDrawCreate(569.000000, 67.000000, "100");
			TextDrawBackgroundColor(PlayerHealth[i], 255);
			TextDrawFont(PlayerHealth[i], 2);
			TextDrawLetterSize(PlayerHealth[i], 0.189999, 0.699998);
			TextDrawColor(PlayerHealth[i], -1);
			TextDrawSetOutline(PlayerHealth[i], 0);
			TextDrawSetProportional(PlayerHealth[i], 1);
			TextDrawSetShadow(PlayerHealth[i], 1);
			TextDrawSetSelectable(PlayerHealth[i], 0);
		}
        return 1;
}

public OnGameModeExit()
{
        return 1;
}

public OnPlayerRequestClass(playerid, classid)
{
        Selection[playerid] = false;
       	SetSpawnInfo(playerid,0,0,0,0,0,0,0,0,0,0,0,0);
		SpawnPlayer(playerid);



        PlayerPlaySound(playerid,1186,0.0,0.0,0.0);

		TextDrawHideForPlayer(playerid,TDEditor_TD[0]);
		TextDrawHideForPlayer(playerid,TDEditor_TD[1]);
		TextDrawHideForPlayer(playerid,TDEditor_TD[2]);

    	SPAWNED[playerid] = false;
        SetPlayerInterior(playerid,1);
        SetPlayerPos(playerid,292.9160,-30.6521,1001.5156);
        SetPlayerCameraPos(playerid,293.1492,-37.2610,1001.5156);
        SetPlayerCameraLookAt(playerid,292.9160,-30.6521,1001.5156);
        SetPlayerFacingAngle(playerid,177.3484);
        
        if(classid == 0)
    	{
        gTeam[playerid] = TEAM_RUS;
        SetPlayerTeam(playerid,0);
        GameTextForPlayer(playerid,"~r~Russia",3000,4);
        //SetPlayerColor(playerid,0xFF000000);
        SetPlayerColor(playerid,0xFF0000FF);

    	}
		else if(classid == 1)
    	{
        gTeam[playerid] = TEAM_USA;
        SetPlayerTeam(playerid,1);
        GameTextForPlayer(playerid,"~b~United States",3000,4);
        //SetPlayerColor(playerid,0x0000FF00);
        SetPlayerColor(playerid,0x0000FFFF);
    	}



        return 1;
}
public OnPlayerKeyStateChange(playerid, newkeys, oldkeys)
{

	return 1;
}
public OnPlayerRequestSpawn(playerid)
{

/*	new team1 = GetPlayersInTeamFromMaxPlayers(TEAM_RUS);//team1 = how many players are in TEAM_RUS..
    new team2 = GetPlayersInTeamFromMaxPlayers(TEAM_USA);//team2 = how many players are in TEAM_USA..
    if(team1 > team2 && gTeam[playerid] == TEAM_RUS)//if team1 has more players than team2 and the player is trying to spawn as TEAM_ONE..
    {
		SendClientMessage(playerid,COLOR_RED,"Teams will be unbalanced! Please choose other team!");//Tell them its full, choose another team..
		return 0;//And stop them from spawning..
    }
    else if(team2 > team1 && gTeam[playerid] == TEAM_USA)//if team2 has more players than team1 and the player is trying to spawn as TEAM_TWO..
    {
		SendClientMessage(playerid,COLOR_BLUE,"Teams will be unbalanced! Please choose other team!");//Tell them its full, choose another team..
		return 0;//And stop them from spawning..
    }
    if(team1 == team2 && gTeam[playerid] == TEAM_RUS)
	{
	    return 1;
	}
	if(team1 == team2 && gTeam[playerid] == TEAM_USA)
	{
	    return 1;
	}*/



        /*if(gTeam[playerid] == TEAM_RUS && gTeam[i] == TEAM_USA)
       	{
			SetPlayerColor(playerid, 0xFF000000);
            SetPlayerColor(i, 0x0000FF00);
            ShowPlayerNameTagForPlayer(playerid,i,0);
   		}
		if(gTeam[playerid] == TEAM_USA && gTeam[i] == TEAM_RUS)
		{
        	SetPlayerColor(playerid, 0x0000FF00);
        	SetPlayerColor(i, 0xFF000000);
        	ShowPlayerNameTagForPlayer(playerid,i,0);
		}
		*/


	if(gTeam[playerid] ==  TEAM_RUS)
	{
	    new string[129];
		new name[MAX_PLAYER_NAME];
		GetPlayerName(playerid,name,sizeof(name));
		format(string,sizeof(string),"%s {FFFFFF}has joined {FF0002}Russia",name);
		SendClientMessageToAll(COLOR_RED,string);
	}
	if(gTeam[playerid] ==  TEAM_USA)
	{
	    new string[129];
		new name[MAX_PLAYER_NAME];
		GetPlayerName(playerid,name,sizeof(name));
		format(string,sizeof(string),"%s {FFFFFF}has joined {0033FF}USA",name);
		SendClientMessageToAll(COLOR_BLUE,string);
	}






	TextDrawShowForPlayer(playerid,Textdraw0);
	TextDrawShowForPlayer(playerid,Textdraw1);
	TextDrawShowForPlayer(playerid,Textdraw2);
	TextDrawShowForPlayer(playerid,Textdraw3);
	TextDrawShowForPlayer(playerid,Textdraw4);
	TextDrawShowForPlayer(playerid,Textdraw5);
	TextDrawShowForPlayer(playerid,Textdraw6);
	TextDrawShowForPlayer(playerid,Textdraw7);
	TextDrawShowForPlayer(playerid,Textdraw8);
	TextDrawShowForPlayer(playerid,Textdraw9);
	TextDrawShowForPlayer(playerid,Textdraw9);
	SelectTextDraw(playerid, 0xA3B4C5FF);
 	return 1;
}

public OnPlayerPickUpPickup(playerid,pickupid)
{
	for(new i=0; i<MAX_PLAYERS; i++)
    {
        if(!IsPlayerConnected(i)) {continue;}
        if(status[i] == 0) {continue;}
		if(pickupid == minePickup[i])
    	{
    	    new Float:X, Float:Y, Float:Z;
    		GetObjectPos(mine[i][0], X, Y, Z);
     		CreateExplosion(X, Y, Z, 7, 1);
    		DestroyPickup(minePickup[i]);
    		DestroyObject(mine[i][0]);
    		DestroyObject(mine[i][1]);
    		status[i] = 0;
	 	}
 	}
 	return 1;
}

public OnPlayerConnect(playerid)
{

        PlayerPlaySound(playerid,1185,0.0,0.0,0.0);

		//obrisi
		IndicatorBox[playerid] = CreatePlayerTextDraw(playerid, 538.444458, 392.753326, "usebox");
        PlayerTextDrawLetterSize(playerid, IndicatorBox[playerid], 0.000000, -2.584319);
        PlayerTextDrawTextSize(playerid, IndicatorBox[playerid], 622.444458, 0.000000);
        PlayerTextDrawAlignment(playerid, IndicatorBox[playerid], 1);
        PlayerTextDrawColor(playerid, IndicatorBox[playerid], 0);
        PlayerTextDrawUseBox(playerid, IndicatorBox[playerid], true);
        PlayerTextDrawBoxColor(playerid, IndicatorBox[playerid], 102);
        PlayerTextDrawSetShadow(playerid, IndicatorBox[playerid], 0);
        PlayerTextDrawFont(playerid, IndicatorBox[playerid], 0);

        Indicator[playerid] = CreatePlayerTextDraw(playerid, 540.000183, 374.826660, "Hit: 4 - 150 dmg");
        PlayerTextDrawLetterSize(playerid, Indicator[playerid], 0.227333, 1.346132);
        PlayerTextDrawAlignment(playerid, Indicator[playerid], 1);
        PlayerTextDrawColor(playerid, Indicator[playerid], -1);
        PlayerTextDrawSetShadow(playerid, Indicator[playerid], 0);
        PlayerTextDrawSetOutline(playerid, Indicator[playerid], 1);
        PlayerTextDrawBackgroundColor(playerid, Indicator[playerid], 51);
        PlayerTextDrawFont(playerid, Indicator[playerid], 1);
        PlayerTextDrawSetProportional(playerid, Indicator[playerid], 1);

		Level[playerid] = CreatePlayerTextDraw(playerid, 7.500000, 414.312500, "Level: 0");
		PlayerTextDrawLetterSize(playerid, Level[playerid], 0.449999, 1.600000);
		PlayerTextDrawAlignment(playerid, Level[playerid], 1);
		PlayerTextDrawColor(playerid, Level[playerid], 1304553727);// STARA JE -5963521
		PlayerTextDrawSetOutline(playerid,Level[playerid], 1);
		PlayerTextDrawBackgroundColor(playerid,Level[playerid], 65535);
		PlayerTextDrawSetShadow(playerid, Level[playerid], 0);
		PlayerTextDrawSetOutline(playerid, Level[playerid], 1);
		PlayerTextDrawBackgroundColor(playerid, Level[playerid], 51);
		PlayerTextDrawFont(playerid, Level[playerid], 1);
		PlayerTextDrawSetProportional(playerid, Level[playerid], 1);

		Rank[playerid] = CreatePlayerTextDraw(playerid, 8.000000, 430.500000, "Rank: Major");
		PlayerTextDrawLetterSize(playerid, Rank[playerid], 0.449999, 1.600000);
		PlayerTextDrawAlignment(playerid, Rank[playerid], 1);
		PlayerTextDrawColor(playerid, Rank[playerid], 1304553727);
		PlayerTextDrawSetOutline(playerid,Rank[playerid], 1);
		PlayerTextDrawBackgroundColor(playerid,Rank[playerid], 65535);
		PlayerTextDrawSetShadow(playerid, Rank[playerid], 0);
		PlayerTextDrawSetOutline(playerid, Rank[playerid], 1);
		PlayerTextDrawBackgroundColor(playerid, Rank[playerid], 51);
		PlayerTextDrawFont(playerid, Rank[playerid], 1);
		PlayerTextDrawSetProportional(playerid, Rank[playerid], 1);

		XP[playerid] = CreatePlayerTextDraw(playerid, 434.500000, 415.625000, "XP:0");
		PlayerTextDrawLetterSize(playerid, XP[playerid], 0.449999, 1.600000);
		PlayerTextDrawAlignment(playerid, XP[playerid], 1);
		PlayerTextDrawColor(playerid, XP[playerid], 1304553727);
		PlayerTextDrawSetOutline(playerid,XP[playerid], 1);
		PlayerTextDrawBackgroundColor(playerid,XP[playerid], 65535);
		PlayerTextDrawSetShadow(playerid, XP[playerid], 0);
		PlayerTextDrawSetOutline(playerid, XP[playerid], 1);
		PlayerTextDrawBackgroundColor(playerid, XP[playerid], 51);
		PlayerTextDrawFont(playerid, XP[playerid], 1);
		PlayerTextDrawSetProportional(playerid, XP[playerid], 1);

		// this down is how much you need for next level
		zanivo[playerid] = CreatePlayerTextDraw(playerid, 522.500000, 415.625000, "/0");
		PlayerTextDrawLetterSize(playerid, zanivo[playerid], 0.449999, 1.600000);
		PlayerTextDrawAlignment(playerid, zanivo[playerid], 1);
		PlayerTextDrawColor(playerid, zanivo[playerid], -1523963137);
		PlayerTextDrawSetShadow(playerid, zanivo[playerid], 0);
		PlayerTextDrawSetOutline(playerid, zanivo[playerid], 1);
		PlayerTextDrawBackgroundColor(playerid, zanivo[playerid], 51);
		PlayerTextDrawFont(playerid, zanivo[playerid], 1);
		PlayerTextDrawSetProportional(playerid, zanivo[playerid], 1);

		TextDrawShowForPlayer(playerid,TDEditor_TD[0]);
		TextDrawShowForPlayer(playerid,TDEditor_TD[1]);
		TextDrawShowForPlayer(playerid,TDEditor_TD[2]);
		//TextDrawShowForPlayer(playerid,TDEditor_TD[3]);

		new levelstring[127];
		format(levelstring,sizeof(levelstring),"Level: %d",PlayerInfo[playerid][pLevel]);
	  	PlayerTextDrawSetString(playerid,Level[playerid],levelstring);

        ReplyId[playerid] = INVALID_PLAYER_ID;
		Spawned[playerid] = 0;
		SetTimerEx("TimeOnServer", 1000, 1, "i", playerid);//time player was on server

		//---------REGENHP-------------//
		SetTimerEx("REGENHP",100,0,"i",playerid);
		//-----------------------------//

		if(fexist(UserPath(playerid)))
    	{
        	INI_ParseFile(UserPath(playerid), "LoadUser_%s", .bExtra = true, .extra = playerid);
        	if(PlayerInfo[playerid][pBanned] == 1)
        	{
        	    SendClientMessage(playerid, 0xFF444499, "You are banned, do not evade.");
        	    Ban(playerid);
        	}
			else
			{
        	ShowPlayerDialog(playerid, DIALOG_LOGIN, DIALOG_STYLE_PASSWORD,""COL_WHITE"Login",""COL_WHITE"Type your password below to login.","Login","Quit");
        	}
    	}
    	else
   		{
   			ShowPlayerDialog(playerid, DIALOG_REGISTER, DIALOG_STYLE_PASSWORD,""COL_WHITE"Registering...",""COL_WHITE"Type your password below to register a new account.","Register","Quit");
    	}
		new playername[MAX_PLAYER_NAME], string[64];
		GetPlayerName(playerid, playername, sizeof(playername));
		format(string, sizeof(string), "%s (%d) has joined.", playername, playerid);
		SendClientMessageToAll(COLOR_GREY, string);
        SetPlayerColor(playerid,-1);
        SPAWNED[playerid] = false;
        SetTimerEx("FloodCheck",3000,true,"i",playerid);
		/*
		TextDrawShowForPlayer(playerid, crnapozadina);
        PlayerTextDrawShow(playerid, Level[playerid]);
        //TextDrawSetString(Level,PlayerInfo[playerid][pLevel]);
        PlayerTextDrawShow(playerid, Rank[playerid]);
        PlayerTextDrawShow(playerid, XP[playerid]);
        PlayerTextDrawShow(playerid, zanivo[playerid]);
        //TextDrawSetString(Level,PlayerInfo[playerid][pRank]);
        TextDrawShowForPlayer(playerid, russkor);
        TextDrawShowForPlayer(playerid, usaskor);
        TextDrawShowForPlayer(playerid, TDtKills);
        TextDrawShowForPlayer(playerid, TDctKills);
        TextDrawShowForPlayer(playerid, Timeleft);
        TextDrawShowForPlayer(playerid, CurrentMap);*/


        return 1;
}

public OnPlayerDisconnect(playerid, reason)
{
        new INI:File = INI_Open(UserPath(playerid));
		INI_SetTag(File,"data");
    	INI_WriteInt(File,"Admin",PlayerInfo[playerid][pAdmin]);
    	INI_WriteInt(File,"Kills",PlayerInfo[playerid][pKills]);
    	INI_WriteInt(File,"Deaths",PlayerInfo[playerid][pDeaths]);
    	INI_WriteInt(File,"Score", PlayerInfo[playerid][pScore]);
    	INI_WriteInt(File,"Banned", PlayerInfo[playerid][pBanned]);
    	INI_WriteInt(File,"Hours",PlayerInfo[playerid][pHour]);
    	INI_WriteInt(File,"Minutes",PlayerInfo[playerid][pMin]);
    	INI_WriteInt(File,"Seconds",PlayerInfo[playerid][pSec]);
    	INI_WriteInt(File,"Level",PlayerInfo[playerid][pLevel]);
    	INI_WriteString(File,"Rank",PlayerInfo[playerid][pRank]);//OVO ODJE
    	INI_WriteInt(File,"XP",PlayerInfo[playerid][pXP]);
    	INI_Close(File);

	    new pname[MAX_PLAYER_NAME], string[39 + MAX_PLAYER_NAME];
    	GetPlayerName(playerid, pname, sizeof(pname));
    	switch(reason)
    	{
        	case 0: format(string, sizeof(string), "%s has left the server. (Lost Connection)", pname);
        	case 1: format(string, sizeof(string), "%s has left the server. (Leaving)", pname);
        	case 2: format(string, sizeof(string), "%s has left the server. (Kicked)", pname);
    	}
    	SendClientMessageToAll(COLOR_GREY, string);

        /*new playername[MAX_PLAYER_NAME], string[64];
		GetPlayerName(playerid, playername, sizeof(playername));
		format(string, sizeof(string), "%s (%d) has disconnected.", playername, playerid);
		SendClientMessageToAll(COLOR_GREY, string);
    	SetPlayerColor(playerid,-1);*/
        SPAWNED[playerid] = false;
        if(AntiSpawnKill[playerid])
		{
		    KillTimer(AntiSpawnKillTimer[playerid]);
			AntiSpawnKill[playerid]=false;
		}
		PlayerInfo[playerid][pRoundkills]=0;
  		PlayerInfo[playerid][pKills] = 0;
        PlayerInfo[playerid][pDeaths] = 0;
        PlayerInfo[playerid][pScore] = 0;
        PlayerInfo[playerid][pHour] = 0;
        PlayerInfo[playerid][pMin] = 0;
        PlayerInfo[playerid][pSec] = 0;
        PlayerInfo[playerid][pXP] = 0;

		if(status[playerid] == 1)
		{
			DestroyPickup(minePickup[playerid]);
			DestroyObject(mine[playerid][0]);
			DestroyObject(mine[playerid][1]);
			status[playerid] = 0;
			KillTimer(timer[playerid]);
        	Delete3DTextLabel(minetext[playerid]);
		}

        return 1;
}
public OnPlayerClickTextDraw(playerid, Text:clickedid)
{
//=======================SUPPORT==============================================//
if(_:clickedid != INVALID_TEXT_DRAW) // If the player clicked a valid textdraw
	{
	if(clickedid == Textdraw2)
	{
		if(GetPlayerTeam(playerid) == 0)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,29,630);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,16,1);
		    SetSpawnInfo(playerid,0,111,0,0,0,0,29,630,23,15000,16,1);
		}
		if(GetPlayerTeam(playerid) == 1)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,29,630);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,16,1);
            SetSpawnInfo(playerid,1,111,0,0,0,0,29,630,23,15000,16,1);
 		}
	}
	if(clickedid == Textdraw5)
	{
		if(GetPlayerTeam(playerid) == 0)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,29,630);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,16,1);
		    SetSpawnInfo(playerid,0,111,0,0,0,0,29,630,23,15000,16,1);
		}
		if(GetPlayerTeam(playerid) == 1)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,29,630);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,16,1);
            SetSpawnInfo(playerid,1,111,0,0,0,0,29,630,23,15000,16,1);
 		}
	}
	if(clickedid == Textdraw8)
	{
		if(GetPlayerTeam(playerid) == 0)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,29,630);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,16,1);
		    SetSpawnInfo(playerid,0,111,0,0,0,0,29,630,23,15000,16,1);
		}
		if(GetPlayerTeam(playerid) == 1)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,29,630);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,16,1);
            SetSpawnInfo(playerid,1,111,0,0,0,0,29,630,23,15000,16,1);
 		}
	}
	//=================================ASSAULT================================//
	if(clickedid == Textdraw3)
	{
		if(GetPlayerTeam(playerid) == 0)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,30,500);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,16,1);
		    SetSpawnInfo(playerid,0,111,0,0,0,0,30,500,23,15000,16,1);
		}
		if(GetPlayerTeam(playerid) == 1)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,31,500);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,16,1);
            SetSpawnInfo(playerid,1,287,0,0,0,0,31,500,23,15000,16,1);
 		}
	}
	if(clickedid == Textdraw6)
	{
	if(GetPlayerTeam(playerid) == 0)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,30,500);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,16,1);
		    SetSpawnInfo(playerid,0,111,0,0,0,0,30,500,23,15000,16,1);
		}
	if(GetPlayerTeam(playerid) == 1)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,31,500);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,16,1);
            SetSpawnInfo(playerid,1,287,0,0,0,0,31,500,23,15000,16,1);
 		}
	}
	if(clickedid == Textdraw9)
	{
		if(GetPlayerTeam(playerid) == 0)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,30,500);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,16,1);
		    SetSpawnInfo(playerid,0,111,0,0,0,0,30,500,23,15000,16,1);
		}
		if(GetPlayerTeam(playerid) == 1)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,31,500);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,16,1);
            SetSpawnInfo(playerid,1,287,0,0,0,0,31,500,23,15000,16,1);
 		}
	}
 //===============================RECON=======================================//
	if(clickedid == Textdraw4)
	{
		if(GetPlayerTeam(playerid) == 0)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,34,50);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,17,2);
		    SetSpawnInfo(playerid,0,111,0,0,0,0,34,50,23,15000,17,2);
		}
		if(GetPlayerTeam(playerid) == 1)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,34,50);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,17,2);
            SetSpawnInfo(playerid,1,111,0,0,0,0,34,50,23,15000,17,2);
 		}
	}
	if(clickedid == Textdraw7)
	{
		if(GetPlayerTeam(playerid) == 0)
		{
		    ResetPlayerWeapons(playerid);
		   	GivePlayerWeapon(playerid,34,50);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,17,2);
		    SetSpawnInfo(playerid,0,111,0,0,0,0,34,50,23,15000,17,2);
		}
		if(GetPlayerTeam(playerid) == 1)
		{
		    ResetPlayerWeapons(playerid);
  			GivePlayerWeapon(playerid,34,50);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,17,2);
            SetSpawnInfo(playerid,1,111,0,0,0,0,34,50,23,15000,17,2);
 		}
	}
	if(clickedid == Textdraw10)
	{
		if(GetPlayerTeam(playerid) == 0)
		{
		    ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,34,50);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,17,2);
		    SetSpawnInfo(playerid,0,111,0,0,0,0,34,50,23,15000,17,2);
		}
		if(GetPlayerTeam(playerid) == 1)
		{
			ResetPlayerWeapons(playerid);
			GivePlayerWeapon(playerid,34,50);
			GivePlayerWeapon(playerid,23,15000);
			GivePlayerWeapon(playerid,17,2);
            SetSpawnInfo(playerid,1,111,0,0,0,0,34,50,23,15000,17,2);
 		}
	}
	if(clickedid == Selection_TD[20])
	{
	    SetPlayerSkin(playerid,111);
	    Selection[playerid]= true;
	    TogglePlayerControllable(playerid,true);
	    gTeam[playerid] = TEAM_RUS;
        SetPlayerTeam(playerid,0);
        GameTextForPlayer(playerid,"~r~Russia",3000,4);
        //SetPlayerColor(playerid,0xFF000000);
        SetPlayerColor(playerid,0xFF0000FF);
   		TextDrawHideForPlayer(playerid,Selection_TD[0]);
		TextDrawHideForPlayer(playerid,Selection_TD[1]);
		TextDrawHideForPlayer(playerid,Selection_TD[2]);
		TextDrawHideForPlayer(playerid,Selection_TD[3]);
		TextDrawHideForPlayer(playerid,Selection_TD[4]);
		TextDrawHideForPlayer(playerid,Selection_TD[5]);
		TextDrawHideForPlayer(playerid,Selection_TD[6]);
		TextDrawHideForPlayer(playerid,Selection_TD[7]);
		TextDrawHideForPlayer(playerid,Selection_TD[8]);
		TextDrawHideForPlayer(playerid,Selection_TD[9]);
		TextDrawHideForPlayer(playerid,Selection_TD[10]);
		TextDrawHideForPlayer(playerid,Selection_TD[11]);
		TextDrawHideForPlayer(playerid,Selection_TD[12]);
		TextDrawHideForPlayer(playerid,Selection_TD[13]);
		TextDrawHideForPlayer(playerid,Selection_TD[14]);
		TextDrawHideForPlayer(playerid,Selection_TD[15]);
		TextDrawHideForPlayer(playerid,Selection_TD[16]);
		TextDrawHideForPlayer(playerid,Selection_TD[17]);
		TextDrawHideForPlayer(playerid,Selection_TD[18]);
		TextDrawHideForPlayer(playerid,Selection_TD[19]);
		TextDrawHideForPlayer(playerid,Selection_TD[20]);
		TextDrawHideForPlayer(playerid,Selection_TD[21]);
		TextDrawHideForPlayer(playerid,Selection_TD[22]);
		TextDrawHideForPlayer(playerid,Selection_TD[23]);
		
		SpawnPlayer(playerid);
		SetPlayerPos(playerid, MapInfo[MAP][mTSpawnX], MapInfo[MAP][mTSpawnY], MapInfo[MAP][mTSpawnZ]);


	}
	if(clickedid == Selection_TD[22])
	{
 		SetPlayerSkin(playerid,287);
	    Selection[playerid]= true;
	    TogglePlayerControllable(playerid,true);
        gTeam[playerid] = TEAM_USA;
        SetPlayerTeam(playerid,1);
        GameTextForPlayer(playerid,"~b~United States",3000,4);
        //SetPlayerColor(playerid,0x0000FF00);
        SetPlayerColor(playerid,0x0000FFFF);
   		TextDrawHideForPlayer(playerid,Selection_TD[0]);
		TextDrawHideForPlayer(playerid,Selection_TD[1]);
		TextDrawHideForPlayer(playerid,Selection_TD[2]);
		TextDrawHideForPlayer(playerid,Selection_TD[3]);
		TextDrawHideForPlayer(playerid,Selection_TD[4]);
		TextDrawHideForPlayer(playerid,Selection_TD[5]);
		TextDrawHideForPlayer(playerid,Selection_TD[6]);
		TextDrawHideForPlayer(playerid,Selection_TD[7]);
		TextDrawHideForPlayer(playerid,Selection_TD[8]);
		TextDrawHideForPlayer(playerid,Selection_TD[9]);
		TextDrawHideForPlayer(playerid,Selection_TD[10]);
		TextDrawHideForPlayer(playerid,Selection_TD[11]);
		TextDrawHideForPlayer(playerid,Selection_TD[12]);
		TextDrawHideForPlayer(playerid,Selection_TD[13]);
		TextDrawHideForPlayer(playerid,Selection_TD[14]);
		TextDrawHideForPlayer(playerid,Selection_TD[15]);
		TextDrawHideForPlayer(playerid,Selection_TD[16]);
		TextDrawHideForPlayer(playerid,Selection_TD[17]);
		TextDrawHideForPlayer(playerid,Selection_TD[18]);
		TextDrawHideForPlayer(playerid,Selection_TD[19]);
		TextDrawHideForPlayer(playerid,Selection_TD[20]);
		TextDrawHideForPlayer(playerid,Selection_TD[21]);
		TextDrawHideForPlayer(playerid,Selection_TD[22]);
		TextDrawHideForPlayer(playerid,Selection_TD[23]);
		
		SpawnPlayer(playerid);
		SetPlayerPos(playerid, MapInfo[MAP][mCTSpawnX], MapInfo[MAP][mCTSpawnY], MapInfo[MAP][mCTSpawnZ]);
	}
	if(clickedid == Selection_TD[21])
	{
 		SetPlayerSkin(playerid,111);
	    Selection[playerid]= true;
	    TogglePlayerControllable(playerid,true);
	    gTeam[playerid] = TEAM_RUS;
        SetPlayerTeam(playerid,0);
        GameTextForPlayer(playerid,"~r~Russia",3000,4);
        //SetPlayerColor(playerid,0xFF000000);
        SetPlayerColor(playerid,0xFF0000FF);
   		TextDrawHideForPlayer(playerid,Selection_TD[0]);
		TextDrawHideForPlayer(playerid,Selection_TD[1]);
		TextDrawHideForPlayer(playerid,Selection_TD[2]);
		TextDrawHideForPlayer(playerid,Selection_TD[3]);
		TextDrawHideForPlayer(playerid,Selection_TD[4]);
		TextDrawHideForPlayer(playerid,Selection_TD[5]);
		TextDrawHideForPlayer(playerid,Selection_TD[6]);
		TextDrawHideForPlayer(playerid,Selection_TD[7]);
		TextDrawHideForPlayer(playerid,Selection_TD[8]);
		TextDrawHideForPlayer(playerid,Selection_TD[9]);
		TextDrawHideForPlayer(playerid,Selection_TD[10]);
		TextDrawHideForPlayer(playerid,Selection_TD[11]);
		TextDrawHideForPlayer(playerid,Selection_TD[12]);
		TextDrawHideForPlayer(playerid,Selection_TD[13]);
		TextDrawHideForPlayer(playerid,Selection_TD[14]);
		TextDrawHideForPlayer(playerid,Selection_TD[15]);
		TextDrawHideForPlayer(playerid,Selection_TD[16]);
		TextDrawHideForPlayer(playerid,Selection_TD[17]);
		TextDrawHideForPlayer(playerid,Selection_TD[18]);
		TextDrawHideForPlayer(playerid,Selection_TD[19]);
		TextDrawHideForPlayer(playerid,Selection_TD[20]);
		TextDrawHideForPlayer(playerid,Selection_TD[21]);
		TextDrawHideForPlayer(playerid,Selection_TD[22]);
		TextDrawHideForPlayer(playerid,Selection_TD[23]);

		SpawnPlayer(playerid);
		SetPlayerPos(playerid, MapInfo[MAP][mTSpawnX], MapInfo[MAP][mTSpawnY], MapInfo[MAP][mTSpawnZ]);
	}
	if(clickedid == Selection_TD[23])
	{
 		SetPlayerSkin(playerid,287);
	    Selection[playerid]= true;
	    TogglePlayerControllable(playerid,true);
        gTeam[playerid] = TEAM_USA;
        SetPlayerTeam(playerid,1);
        GameTextForPlayer(playerid,"~b~United States",3000,4);
        //SetPlayerColor(playerid,0x0000FF00);
        SetPlayerColor(playerid,0x0000FFFF);
   		TextDrawHideForPlayer(playerid,Selection_TD[0]);
		TextDrawHideForPlayer(playerid,Selection_TD[1]);
		TextDrawHideForPlayer(playerid,Selection_TD[2]);
		TextDrawHideForPlayer(playerid,Selection_TD[3]);
		TextDrawHideForPlayer(playerid,Selection_TD[4]);
		TextDrawHideForPlayer(playerid,Selection_TD[5]);
		TextDrawHideForPlayer(playerid,Selection_TD[6]);
		TextDrawHideForPlayer(playerid,Selection_TD[7]);
		TextDrawHideForPlayer(playerid,Selection_TD[8]);
		TextDrawHideForPlayer(playerid,Selection_TD[9]);
		TextDrawHideForPlayer(playerid,Selection_TD[10]);
		TextDrawHideForPlayer(playerid,Selection_TD[11]);
		TextDrawHideForPlayer(playerid,Selection_TD[12]);
		TextDrawHideForPlayer(playerid,Selection_TD[13]);
		TextDrawHideForPlayer(playerid,Selection_TD[14]);
		TextDrawHideForPlayer(playerid,Selection_TD[15]);
		TextDrawHideForPlayer(playerid,Selection_TD[16]);
		TextDrawHideForPlayer(playerid,Selection_TD[17]);
		TextDrawHideForPlayer(playerid,Selection_TD[18]);
		TextDrawHideForPlayer(playerid,Selection_TD[19]);
		TextDrawHideForPlayer(playerid,Selection_TD[20]);
		TextDrawHideForPlayer(playerid,Selection_TD[21]);
		TextDrawHideForPlayer(playerid,Selection_TD[22]);
		TextDrawHideForPlayer(playerid,Selection_TD[23]);

		SpawnPlayer(playerid);
		SetPlayerPos(playerid, MapInfo[MAP][mCTSpawnX], MapInfo[MAP][mCTSpawnY], MapInfo[MAP][mCTSpawnZ]);
	}
//========================================================================//
    TextDrawHideForPlayer(playerid, Textdraw0);
    TextDrawHideForPlayer(playerid, Textdraw1);
    TextDrawHideForPlayer(playerid, Textdraw2);
    TextDrawHideForPlayer(playerid, Textdraw3);
    TextDrawHideForPlayer(playerid, Textdraw4);
    TextDrawHideForPlayer(playerid, Textdraw5);
    TextDrawHideForPlayer(playerid, Textdraw6);
    TextDrawHideForPlayer(playerid, Textdraw7);
    TextDrawHideForPlayer(playerid, Textdraw8);
    TextDrawHideForPlayer(playerid, Textdraw9);
    TextDrawHideForPlayer(playerid, Textdraw10);
	CancelSelectTextDraw(playerid);
 	}
return 1;
}
public OnPlayerSpawn(playerid)
{
    if(Selection[playerid]==false)
    {
        ShowCustomClass(playerid);//Stock we are just going to create
        return 0;
    }
	for(new i = 0; i < MAX_PLAYERS; i ++)
	{
		SetTimerEx("LevelUpdating", 5000, true, "i", i);
	}

 	Dead[playerid] = 0;
 	Spawned[playerid] = 1;

	TextDrawShowForPlayer(playerid, crnapozadina);
	PlayerTextDrawShow(playerid, Level[playerid]);
	//TextDrawSetString(Level,PlayerInfo[playerid][pLevel]);
	PlayerTextDrawShow(playerid, Rank[playerid]);
	PlayerTextDrawShow(playerid, XP[playerid]);
	PlayerTextDrawShow(playerid, zanivo[playerid]);
	//TextDrawSetString(Level,PlayerInfo[playerid][pRank]);
	TextDrawShowForPlayer(playerid, russkor);
	TextDrawShowForPlayer(playerid, usaskor);
	TextDrawShowForPlayer(playerid, TDtKills);
	TextDrawShowForPlayer(playerid, TDctKills);
	TextDrawShowForPlayer(playerid, Timeleft);
	TextDrawShowForPlayer(playerid, CurrentMap);

	if(gTeam[playerid] == TEAM_USA)
	{
	    SetPlayerSkin(playerid,287);
	}
	else if(gTeam[playerid] == TEAM_RUS)
	{
	    SetPlayerSkin(playerid,111);
	}

	SetPlayerInterior(playerid, 0);
	if(GetPlayerTeam(playerid)==0)
	{
	    SetPlayerPos(playerid, MapInfo[MAP][mTSpawnX], MapInfo[MAP][mTSpawnY], MapInfo[MAP][mTSpawnZ]);
	}
	if(GetPlayerTeam(playerid)==1)
	{
	    SetPlayerPos(playerid, MapInfo[MAP][mCTSpawnX], MapInfo[MAP][mCTSpawnY], MapInfo[MAP][mCTSpawnZ]);
	}
 	SetPlayerHealth(playerid,100.0);
    SPAWNED[playerid] = true;
    AntiSpawnKill[playerid]=true;
    AntiSpawnKillTimer[playerid]=SetTimerEx("AntiSpawnKillTimerCallback", 5000, false, "i", playerid);
    SendClientMessage(playerid, COLOR_GREEN, "You are under spawn kill protection for 5 seconds.");
    TextDrawSetString(PlayerHealth[playerid], "100");
    TextDrawShowForPlayer(playerid, PlayerHealth[playerid]);

	for(new i=0;i<MAX_PLAYERS;i++)
	{
	    if(GetPlayerTeam(playerid) == GetPlayerTeam(i))
		{
			SetPlayerMarkerForPlayer(playerid, i, (GetPlayerColor(i) | 0xFF0000FF));
			SetPlayerMarkerForPlayer(i, playerid, (GetPlayerColor(playerid) | 0xFF0000FF));
		}
	}

    return 1;
}

forward AntiSpawnKillTimerCallback(playerid);
public AntiSpawnKillTimerCallback(playerid)
{
	AntiSpawnKill[playerid]=false;
	SendClientMessage(playerid, COLOR_ORANGE, "You are no longer under spawn kill protection.");
	SetPlayerHealth(playerid,100);

}

public OnPlayerDeath(playerid, killerid, reason)
{

    PlayerPlaySound(killerid,1138,0.0,0.0,0.0);

	for(new i = 0; i < MAX_PLAYERS; i++)
	{
		if(playerid != INVALID_PLAYER_ID && killerid != INVALID_PLAYER_ID)
		{
			if(assistkill[i] == playerid && i != killerid && assist[i] >= 30)// bilo je 70
 			{
					assistkill[i] = INVALID_PLAYER_ID;
					assist[i] = 0.0;
					TextDrawSetString(assisttext,"Assist: 50 EXP");
					TextDrawShowForPlayer(i,assisttext);
					SetTimerEx("AssistTime", 5000, false, "i", i);
					PlayerInfo[killerid][pXP] = PlayerInfo[killerid][pXP] + 50;
	 		}
		 }
  	}
	if(killerid != INVALID_PLAYER_ID && GetPlayerWeapon(killerid) != WEAPON_SNIPER)
	{
	 	PlayerInfo[killerid][pXP] = PlayerInfo[killerid][pXP] + 100;
		TextDrawShowForPlayer(killerid,killtext);
		SetTimerEx("KillTime", 5000, false, "i", killerid);
	}
 /*
	if(killerid != INVALID_PLAYER_ID)
	{
		new Float:px, Float:py, Float:pz;
		GetPlayerPos(playerid, px, py, pz);
		dropammo = CreatePickup(2061,3,px,py,pz,0);
		SetTimerEx("DestroyAmmo", 10000, false, "i", playerid);
	}*/


	FirstBlood++;
	if(FirstBlood == 1)
	{
			if(gTeam[killerid] == TEAM_RUS)
			{
				new Name[128];
	    		GetPlayerName(killerid, Name, sizeof(Name));
				new string[128];
			 	format(string, sizeof(string),"{FF0000} %s {FFFFFF} has drawn {FF0000}First Blood!",Name);
		    	SendClientMessageToAll(COLOR_WHITE,string);
		    	PlayAudioStreamForPlayer(killerid, "http://battlefield.nmc-gaming.xyz/sounds/music/First-Blood-Sound.mp3");
	    	}
			else if(gTeam[killerid] == TEAM_USA)
			{
				new Name[128];
	    		GetPlayerName(killerid, Name, sizeof(Name));
				new string[128];
			 	format(string, sizeof(string),"{0000FF} %s {FFFFFF} has drawn {0000FF}First Blood!",Name);
		    	SendClientMessageToAll(COLOR_WHITE,string);
		    	PlayAudioStreamForPlayer(killerid, "http://battlefield.nmc-gaming.xyz/sounds/music/First-Blood-Sound.mp3");
	    	}
	}

	KillingSpree[killerid]++;
	KillingSpree[playerid] = 0;
	if(KillingSpree[killerid] == 3)
	{
 		new Name[128];
    	GetPlayerName(killerid, Name, sizeof(Name));
		new string[128];
	 	format(string, sizeof(string),"{F7B62A} %s {FFFFFF} is on {F7B62A}Killing spree!",Name);
    	SendClientMessageToAll(COLOR_WHITE,string);
    	PlayAudioStreamForPlayer(killerid, "http://battlefield.nmc-gaming.xyz/sounds/music/Unreal-Tournament-Killing-Spree-Sound.mp3");
	}
 	else if(KillingSpree[killerid] == 5)
	{
 		new Name[128];
    	GetPlayerName(killerid, Name, sizeof(Name));
	    new string[128];
     	format(string, sizeof(string),"{F7B62A} %s {FFFFFF} is {F7B62A}Unstoppable!",Name);
    	SendClientMessageToAll(COLOR_WHITE,string);
    	PlayAudioStreamForPlayer(killerid, "http://battlefield.nmc-gaming.xyz/sounds/music/UNSTOPPABLE!-Quake-3-Arena-Sound-Effect.mp3");
	}
	else if(KillingSpree[killerid] == 7)
	{
 		new Name[128];
    	GetPlayerName(killerid, Name, sizeof(Name));
	    new string[128];
     	format(string, sizeof(string),"{F7B62A} %s {FFFFFF} is {F7B62A}Godlike!",Name);
    	SendClientMessageToAll(COLOR_WHITE,string);
    	PlayAudioStreamForPlayer(killerid, "http://battlefield.nmc-gaming.xyz/sounds/music/GODLIKE!-Quake-3-Arena-Sound-Effect.mp3");
	}
	else if(KillingSpree[killerid] == 10)
	{
 		new Name[128];
    	GetPlayerName(killerid, Name, sizeof(Name));
	    new string[128];
     	format(string, sizeof(string),"{F7B62A}HOLY SHIT{FFFFFF}!{F7B62A} %s {FFFFFF}has 10 kills!",Name);
    	SendClientMessageToAll(COLOR_WHITE,string);
    	PlayAudioStreamForPlayer(killerid, "http://battlefield.nmc-gaming.xyz/sounds/music/Unreal-Tournament-Holy-Shit-Sound.mp3");
	}


	Spawned[playerid] = 0;
    Dead[playerid] = 1;
	if(IsPlayerConnected(killerid))
	{
   		PlayerInfo[killerid][pKills]++;
   		PlayerInfo[playerid][pDeaths]++;
	    SendDeathMessage(killerid, playerid, reason);
		if(IsPlayerConnected(killerid))
		{
	   		if(GetPlayerTeam(playerid)!=GetPlayerTeam(killerid))
	    	{
				new string[20];
				if(GetPlayerTeam(killerid)==0)
	        	{
	         	    tKills++;
	        	    format(string, sizeof(string), "RUS : %d", tKills);
	        	    TextDrawSetString(TDtKills, string);
	       	  	    TextDrawShowForAll(TDtKills);
				}
				if(GetPlayerTeam(killerid)==1)
				{
	   				ctKills++;
				    format(string, sizeof(string), "USA : %d", ctKills);
	    	        TextDrawSetString(TDctKills, string);
	     	        TextDrawShowForAll(TDctKills);
				}
			}
			PlayerInfo[killerid][pCash]=PlayerInfo[killerid][pCash]+300;
			SetPlayerScore(killerid, PlayerInfo[killerid][pLevel]);
			ResetPlayerMoney(killerid);
			GivePlayerMoney(killerid, PlayerInfo[killerid][pCash]);
			PlayerInfo[killerid][pRoundkills]++;
		}
	}
	if(AntiSpawnKill[playerid])
	{
	    KillTimer(AntiSpawnKillTimer[playerid]);
		AntiSpawnKill[playerid]=false;
	}
    return 1;
}

public OnPlayerText(playerid, text[])
{
	new sendername[MAX_PLAYER_NAME];
    GetPlayerName(playerid,sendername,sizeof(sendername));
    new string[256];
    if(GetPlayerTeam(playerid) == 0)
    {
		format(string,sizeof(string),"{FFFFFF}[MAIN CHAT] {FF0000}%s: {FFFFFF}%s",sendername,text);
        SendClientMessageToAll(-1,string);
    }
    else if(GetPlayerTeam(playerid) == 1)
    {
        format(string,sizeof(string),"{FFFFFF}[MAIN CHAT] {124DFF}%s: {FFFFFF}%s",sendername,text);
        SendClientMessageToAll(-1,string);
    }
   	new PlayerName[MAX_PLAYER_NAME];
	GetPlayerName(playerid, PlayerName, sizeof(PlayerName));
    return 0;
}

public OnPlayerCommandText(playerid, cmdtext[])
{

 return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
}
public OnPlayerCommandPerformed(playerid, cmdtext[], success)
{
    if (!success)
    {
       SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
    }
    return 1;
}

public OnPlayerUpdate(playerid)
{


	/* ------------------------------------------------------------------------------------------------*/

	/* ------------------------------------------------------------------------------------------------*/
    RemovePlayerAttachedObject(playerid, 0);
    switch (GetPlayerWeapon(playerid))
	{
    	case 23:
		{
  			/*if (IsPlayerAiming(playerid))
  			{
            	if (GetPlayerSpecialAction(playerid) != SPECIAL_ACTION_DUCK)
				{
				    if(GetPlayerTeam(playerid)==0)
					{
						SetPlayerAttachedObject(playerid, 0, 18643, 6,0.108249, 0.030232, 0.118051, 1.468254, 350.512573, 364.284240);
					}
					else
					{
						SetPlayerAttachedObject(playerid, 0, 19080, 6,0.108249, 0.030232, 0.118051, 1.468254, 350.512573, 364.284240);
					}
				}
				else
				{
					if(GetPlayerTeam(playerid)==0)
					{
						SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.108249, 0.030232, 0.118051, 1.468254, 349.862579, 364.784240);
					}
					else
					{
						SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.108249, 0.030232, 0.118051, 1.468254, 349.862579, 364.784240);
					}
				}
    		}
			else
			{
            	if (GetPlayerSpecialAction(playerid) != SPECIAL_ACTION_DUCK)
				{
					if(GetPlayerTeam(playerid)==0)
					{
				     	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.078248, 0.027239, 0.113051, -11.131746, 350.602722, 362.384216);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.078248, 0.027239, 0.113051, -11.131746, 350.602722, 362.384216);
					}
    			}
				else
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
                    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.078248, 0.027239, 0.113051, -11.131746, 350.602722, 362.384216);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.078248, 0.027239, 0.113051, -11.131746, 350.602722, 362.384216);
					}
    			}
			}
		}
		case 27:
		{
        	if (IsPlayerAiming(playerid))
			{
				if (GetPlayerSpecialAction(playerid) != SPECIAL_ACTION_DUCK)
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
                    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.588246, -0.022766, 0.138052, -11.531745, 347.712585, 352.784271);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.588246, -0.022766, 0.138052, -11.531745, 347.712585, 352.784271);
					}
              	}
			  	else
			  	{
			  	    if(GetPlayerTeam(playerid)==0)
			  	    {
			  	    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.588246, -0.022766, 0.138052, 1.468254, 350.712585, 352.784271);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.588246, -0.022766, 0.138052, 1.468254, 350.712585, 352.784271);
					}
				}
        	}
			else
			{
            	if (GetPlayerSpecialAction(playerid) != SPECIAL_ACTION_DUCK)
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
				    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.563249, -0.01976, 0.134051, -11.131746, 351.602722, 351.384216);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.563249, -0.01976, 0.134051, -11.131746, 351.602722, 351.384216);
					}
               	}
	   			else
		   		{
		   		    if(GetPlayerTeam(playerid)==0)
		   		    {
		   		    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.563249, -0.01976, 0.134051, -11.131746, 351.602722, 351.384216);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.563249, -0.01976, 0.134051, -11.131746, 351.602722, 351.384216);
					}
     			}
			}
		}
        case 30:
		{
        	if (IsPlayerAiming(playerid))
			{
            	if (GetPlayerSpecialAction(playerid) != SPECIAL_ACTION_DUCK)
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
                    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.628249, -0.027766, 0.078052, -6.621746, 352.552642, 355.084289);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.628249, -0.027766, 0.078052, -6.621746, 352.552642, 355.084289);
					}
   				}
		   		else
		   		{
					if(GetPlayerTeam(playerid)==0)
					{
						SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.628249, -0.027766, 0.078052, -1.621746, 356.202667, 355.084289);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.628249, -0.027766, 0.078052, -1.621746, 356.202667, 355.084289);
					}
            	}
        	}
			else
			{
             	if (GetPlayerSpecialAction(playerid) != SPECIAL_ACTION_DUCK)
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
                    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.663249, -0.02976, 0.080051, -11.131746, 358.302734, 353.384216);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.663249, -0.02976, 0.080051, -11.131746, 358.302734, 353.384216);
					}
                }
				else
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
                    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.663249, -0.02976, 0.080051, -11.131746, 358.302734, 353.384216);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.663249, -0.02976, 0.080051, -11.131746, 358.302734, 353.384216);
					}
              	}
			}
		}
        case 31:
		{
        	if (IsPlayerAiming(playerid))
			{
            	if (GetPlayerSpecialAction(playerid) != SPECIAL_ACTION_DUCK)
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
                    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.528249, -0.020266, 0.068052, -6.621746, 352.552642, 355.084289);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.528249, -0.020266, 0.068052, -6.621746, 352.552642, 355.084289);
					}
               	}
		   		else
	   			{
	   			    if(GetPlayerTeam(playerid)==0)
	   			    {
                    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.528249, -0.020266, 0.068052, -1.621746, 356.202667, 355.084289);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.528249, -0.020266, 0.068052, -1.621746, 356.202667, 355.084289);
					}
            	}
        	}
			else
			{
            	if (GetPlayerSpecialAction(playerid) != SPECIAL_ACTION_DUCK)
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
				    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.503249, -0.02376, 0.065051, -11.131746, 357.302734, 354.484222);
				    }
				    else
				    {
				        SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.503249, -0.02376, 0.065051, -11.131746, 357.302734, 354.484222);
					}
              	}
			  	else
			  	{
			  	    if(GetPlayerTeam(playerid)==0)
			  	    {
                    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.503249, -0.02376, 0.065051, -11.131746, 357.302734, 354.484222);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.503249, -0.02376, 0.065051, -11.131746, 357.302734, 354.484222);
					}
     			}
			}
		}
		case 34:
		{
			if (IsPlayerAiming(playerid))
			{
				if (GetPlayerSpecialAction(playerid) != SPECIAL_ACTION_DUCK)
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
						SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.528249, -0.020266, 0.068052, -6.621746, 352.552642, 355.084289);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.528249, -0.020266, 0.068052, -6.621746, 352.552642, 355.084289);
					}
				}
				else
				{
					if(GetPlayerTeam(playerid)==0)
					{
						SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.528249, -0.020266, 0.068052, -1.621746, 356.202667, 355.084289);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.528249, -0.020266, 0.068052, -1.621746, 356.202667, 355.084289);
					}
				}
			}
			else
			{
				if (GetPlayerSpecialAction(playerid) != SPECIAL_ACTION_DUCK)
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
						SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.658248, -0.03276, 0.133051, -11.631746, 355.302673, 353.584259);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.658248, -0.03276, 0.133051, -11.631746, 355.302673, 353.584259);
					}
				}
				else
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
						SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.658248, -0.03276, 0.133051, -11.631746, 355.302673, 353.584259);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.658248, -0.03276, 0.133051, -11.631746, 355.302673, 353.584259);
					}
				}
			}
		}
        case 29:
		{
        	if (IsPlayerAiming(playerid))
			{
            	if (GetPlayerSpecialAction(playerid) != SPECIAL_ACTION_DUCK)
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
                    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.298249, -0.02776, 0.158052, -11.631746, 359.302673, 357.584259);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.298249, -0.02776, 0.158052, -11.631746, 359.302673, 357.584259);
					}
				}
                else
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
				    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.298249, -0.02776, 0.158052, 8.368253, 358.302673, 352.584259);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.298249, -0.02776, 0.158052, 8.368253, 358.302673, 352.584259);
					}
             	}
         	}
		 	else
	 		{
            	if (GetPlayerSpecialAction(playerid) != SPECIAL_ACTION_DUCK)
				{
				    if(GetPlayerTeam(playerid)==0)
				    {
                    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.293249, -0.027759, 0.195051, -12.131746, 354.302734, 352.484222);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.293249, -0.027759, 0.195051, -12.131746, 354.302734, 352.484222);
					}
                }
				else
				{
				    if(GetPlayerWeapon(playerid)==0)
				    {
                    	SetPlayerAttachedObject(playerid, 0, 18643, 6, 0.293249, -0.027759, 0.195051, -12.131746, 354.302734, 352.484222);
					}
					else
					{
					    SetPlayerAttachedObject(playerid, 0, 19080, 6, 0.293249, -0.027759, 0.195051, -12.131746, 354.302734, 352.484222);
					}
        		}
			}
		*/}
	}
	new string[8], Float:health;
	GetPlayerHealth(playerid, health);
	format(string, sizeof(string), "%.0f", health);
	TextDrawSetString(PlayerHealth[playerid], string);
	TextDrawShowForPlayer(playerid, PlayerHealth[playerid]);
    return 1;
}
public OnPlayerGiveDamage(playerid, damagedid, Float: amount, weaponid, bodypart)
{
	if(IsPlayerConnected(damagedid) && GetPlayerTeam(playerid)!=GetPlayerTeam(damagedid))
	{
		if(GetPlayerWeapon(playerid) == 34 && bodypart == BODY_PART_HEAD)
		{
		SetPlayerHealth(damagedid,0);
		TextDrawShowForPlayer(playerid,headshottext);
		PlayerInfo[playerid][pXP] = PlayerInfo[playerid][pXP] +130;
		SetTimerEx("HeadshotTime", 5000, false, "i", playerid);
		PlayAudioStreamForPlayer(playerid,"http://nmc-gaming.xyz/battlefield/sounds/Headshot!-Sound-Effect.mp3");
		}
	}
	if(GetPlayerWeapon(playerid) == WEAPON_SNIPER && bodypart!= BODY_PART_HEAD)
	{
		new Float:health;
		GetPlayerHealth(damagedid,health);
		if(health <= 0.0)
		{
			PlayerInfo[playerid][pXP] = PlayerInfo[playerid][pXP] +100;
			TextDrawShowForPlayer(playerid,killtext);
			SetTimerEx("KillTime", 5000, false, "i", playerid);
		}


	}

    return 1;
}
public OnPlayerTakeDamage(playerid, issuerid, Float: amount, weaponid, bodypart)
{
		//obrisi
        if(issuerid != INVALID_PLAYER_ID)
    	{
            if(timerdamage[playerid] != 0) KillTimer(timerdamage[playerid]);

            hit[playerid]++;
            damage[playerid] += amount;

            timerdamage[playerid] = SetTimerEx("DamageReset", 3000, 0, "i", playerid);

            PlayerTextDrawShow(issuerid, IndicatorBox[issuerid]);//playerid x2
            PlayerTextDrawShow(issuerid, Indicator[issuerid]);//playerid x2

            new str[20];
            format(str,sizeof(str),"%d hit(s) %.0f dmg", hit[issuerid], damage[issuerid]);//playerid x2
            PlayerTextDrawSetString(issuerid, Indicator[issuerid], str);//playerid x2
        }

		Damaged[playerid] = 1;
        SetTimerEx("UNDMG", 10000, false, "i",playerid);

		if(issuerid != INVALID_PLAYER_ID)
   		{
   			if(GetPlayerTeam(issuerid) != NO_TEAM && IsPlayerConnected(playerid) && GetPlayerTeam(issuerid) != GetPlayerTeam(playerid))
      		{
      			assistkill[issuerid] = playerid;
        		assist[issuerid] += amount;
      		}
   		}

		if(IsPlayerConnected(issuerid) && GetPlayerTeam(playerid)!=GetPlayerTeam(issuerid))
		{

			PlayerPlaySound(issuerid,17802,0.0,0.0,0.0);
        	new string[16];
			format(string, sizeof(string), "~r~-%.0f", amount);
    		GameTextForPlayer(issuerid, string, 1000, 5);
		}
	    return 1;
}

public OnPlayerWeaponShot(playerid, weaponid, hittype, hitid, Float:fX, Float:fY, Float:fZ)
{
	if(hittype==BULLET_HIT_TYPE_PLAYER)
	{
		if(AntiSpawnKill[hitid] && GetPlayerTeam(playerid)!=GetPlayerTeam(hitid))
		{
		    GameTextForPlayer(playerid, "~r~ANTI SPAWN KILL", 1000, 6);
			return 0;
		}
	}
	return 1;
}

public Second()
{
        new timestr[32];
        seconds--;
        if(seconds <= 0)
        {
                if(timeleft <= 0)
                {
                        seconds = 0;
                        timeleft = 0;
                        FreezeAll();
                        SendClientMessageToAll(-1,"SERVER: {F9E8B7}Changing map...");
                        MAP++;
                        if(MAP == mapid) MAP=0;
                        SetTimer("ShowTop3",5000,false);
                        KillTimer(maptimer);
                        if(tKills==ctKills)
                        {
                            TextDrawBoxColor(WinnerBackground1, 16711935);
                            TextDrawShowForAll(WinnerBackground1);
                            TextDrawShowForAll(WinnerBackground2);
							TextDrawSetString(Winningteam, "Draw match!");
							TextDrawShowForAll(Winningteam);
						}
						else if(tKills>ctKills)
                        {
                            TextDrawBoxColor(WinnerBackground1, -16776961);
                            TextDrawShowForAll(WinnerBackground1);
                            TextDrawShowForAll(WinnerBackground2);
							TextDrawSetString(Winningteam, "Russia has won the round.");
							TextDrawShowForAll(Winningteam);
							for(new i=0; i<MAX_PLAYERS; i++)
							{
								//PlayAudioStreamForPlayer(i,"http://k003.kiwi6.com/hotlink/hmvr60e76w/Terrorist_win_sound_w_download.mp3");
							}

						}
						else
						{
						    TextDrawBoxColor(WinnerBackground1, 65535);
						    TextDrawShowForAll(WinnerBackground1);
                            TextDrawShowForAll(WinnerBackground2);
							TextDrawSetString(Winningteam, "United States has won the round");
							TextDrawShowForAll(Winningteam);
							for(new i=0;i<MAX_PLAYERS;i++)
							{
								//PlayAudioStreamForPlayer(i,"http://k003.kiwi6.com/hotlink/5jvoup6ont/Counter_Terrorists_Win.mp3");
							}
						}
						new playername[MAX_PLAYER_NAME], string[128], p, Kills, bool:MultipleMVPs=false;
						for(new i=0;i<MAX_PLAYERS;i++)
						{
						    if(PlayerInfo[i][pRoundkills]>Kills)
						    {
						        Kills=PlayerInfo[i][pRoundkills];
						        p=i;
							}
						}
						for(new i=0;i<MAX_PLAYERS;i++)
						{
						    if(PlayerInfo[i][pRoundkills]==Kills && p!=i)
						    {
						        MultipleMVPs=true;
							}
							/*if(PlayerInfo[playerid][pRoundkills] > PlayerInfo[i][pRoundkills])
							{

							}*/
						}
						if(!MultipleMVPs)
						{
							GetPlayerName(p, playername, sizeof(playername));
							format(string, sizeof(string), "MVP: %s (%d) with %d kill(s)!", playername, p, Kills);
							TextDrawSetString(MVP, string);
							TextDrawShowForAll(MVP);
						}
						else
						{
							format(string, sizeof(string), /*"MVP: There are multiple MVPs with %d kill(s)!"*/"There are no MVP'S in this round!", playername, Kills);
							TextDrawSetString(MVP, string);
							TextDrawShowForAll(MVP);
						}
						if(timeleft == 10)
						{
		    				TextDrawColor(Timeleft, COLOR_RED);
						}
                }
                else
                {
                seconds = 59;
                timeleft--;
                }
        }
        format(timestr,sizeof(timestr),"%02d:%02d",timeleft,seconds);
        TextDrawSetString(Timeleft,timestr);
        return 1;
}

public ShowTop3(playerid)
{
	TextDrawHideForAll(WinnerBackground1);
	TextDrawHideForAll(WinnerBackground2);
	TextDrawHideForAll(Winningteam);
	TextDrawHideForAll(MVP);
	
	new top1, top2, top3;
	for(new i=0;i<MAX_PLAYERS;i++)
	{
	    if(IsPlayerConnected(i))
	    {
	        if(PlayerInfo[i][pRoundkills]>PlayerInfo[top1][pRoundkills]) top1 = i;
		}
	}
	if(PlayerInfo[top1][pRoundkills]==0) return CallLocalFunction("ChangeMap", "");
	for (new i=0; i< MAX_PLAYERS; i++)
	{
	    if(IsPlayerConnected(i) && top1!=i)
	    {
	        if(PlayerInfo[i][pRoundkills]>PlayerInfo[top2][pRoundkills]) top2 = i;
		}
	}
	if(PlayerInfo[top2][pRoundkills]==0) top2=INVALID_PLAYER_ID;
	for (new i=0; i< MAX_PLAYERS; i++)
	{
	    if(IsPlayerConnected(i) && top1!=i && top2!=i)
	    {
	        if(PlayerInfo[i][pRoundkills]>PlayerInfo[top3][pRoundkills] && PlayerInfo[i][pRoundkills]!=0) top3 = i;
		}
	}
	if(PlayerInfo[top3][pRoundkills]==0) top3=INVALID_PLAYER_ID;
	
	Top3Objects[0]=CreateObject(3437,1441.0000000,-973.0000000,51.6000000,0.0000000,0.0000000,0.0000000); //object(ballypllr01_lvs) (2)
	Top3Objects[1]=CreateObject(3437,1444.5000000,-973.0000000,52.8000000,0.0000000,0.0000000,0.0000000); //object(ballypllr01_lvs) (4)
	Top3Objects[2]=CreateObject(3437,1448.0000000,-973.0000000,51.6000000,0.0000000,0.0000000,0.0000000); //object(ballypllr01_lvs) (5)
	Top3Objects[3]=CreateObject(2780,1451.1000000,-967.9000200,51.8000000,0.0000000,0.0000000,0.0000000); //object(cj_smoke_mach) (6)
	Top3Objects[4]=CreateObject(2780,1444.4000000,-968.0999800,51.8000000,0.0000000,0.0000000,0.0000000); //object(cj_smoke_mach) (7)
	Top3Objects[5]=CreateObject(2780,1440.9000000,-968.2000100,51.8000000,0.0000000,0.0000000,0.0000000); //object(cj_smoke_mach) (8)
	Top3Objects[6]=CreateObject(2780,1436.9000000,-968.2999900,51.8000000,0.0000000,0.0000000,0.0000000); //object(cj_smoke_mach) (9)
	Top3Objects[7]=CreateObject(2780,1433.2000000,-968.4000200,51.8000000,0.0000000,0.0000000,0.0000000); //object(cj_smoke_mach) (10)
	Top3Objects[8]=CreateObject(2780,1430.2000000,-968.5000000,51.8000000,0.0000000,0.0000000,0.0000000); //object(cj_smoke_mach) (11)
	Top3Objects[9]=CreateObject(2780,1457.9000000,-967.5000000,51.8000000,0.0000000,0.0000000,0.0000000); //object(cj_smoke_mach) (12)
	Top3Objects[10]=CreateObject(2780,1461.7000000,-956.7000100,51.8000000,0.0000000,0.0000000,0.0000000); //object(cj_smoke_mach) (13)
	Top3Objects[11]=CreateObject(2780,1449.5000000,-963.4000200,51.8000000,0.0000000,0.0000000,0.0000000); //object(cj_smoke_mach) (14)
	Top3Objects[12]=CreateObject(2780,1457.3000000,-964.4000200,51.8000000,0.0000000,0.0000000,0.0000000); //object(cj_smoke_mach) (15)
	Top3Objects[13]=CreateObject(2780,1433.3000000,-964.5000000,51.8000000,0.0000000,0.0000000,0.0000000); //object(cj_smoke_mach) (16)
	Top3Objects[14]=CreateObject(2780,1452.9000000,-962.7999900,51.8000000,0.0000000,0.0000000,0.0000000); //object(cj_smoke_mach) (17)
	Top3Objects[15]=CreateObject(3461,1437.5000000,-972.7000100,58.0000000,0.0000000,0.0000000,0.0000000); //object(tikitorch01_lvs) (1)
	Top3Objects[16]=CreateObject(3461,1451.5000000,-972.7000100,58.0000000,0.0000000,0.0000000,0.0000000); //object(tikitorch01_lvs) (2)
	Top3Objects[17]=CreateObject(1271,1445.1000000,-983.9000200,56.9000000,0.0000000,0.0000000,0.0000000); //object(gunbox) (1)

	for(new i;i<MAX_PLAYERS;i++)
	{
	    if(IsPlayerConnected(i))
	    {
	        SetPlayerCameraPos(playerid,1444.5592,-980.7323,57.5156);
			SetPlayerCameraLookAt(playerid,1444.4711,-973.8677,57.5156);
		}
	}
	SetPlayerPos(top1,1444.5739,-972.7752,59.6679);
	SetPlayerFacingAngle(top1,179.5416);
	SetPlayerPos(top2,1441.0249,-973.0031,58.4679);
	SetPlayerFacingAngle(top2,179.5416);
	SetPlayerPos(top3,1441.0249,-973.0031,58.4679);
	SetPlayerFacingAngle(top3,179.5416);
	SetTimer("ChangeMap", 8000, false);
	return 1;
}
forward AssistTime(i);
public AssistTime(i)
{
	TextDrawHideForPlayer(i,assisttext);
}

forward KillTime(killerid);
public KillTime(killerid)
{
	TextDrawHideForPlayer(killerid,killtext);
}

forward HeadshotTime(issuerid);
public HeadshotTime(issuerid)
{
	TextDrawHideForPlayer(issuerid,headshottext);
}
forward BanTime(id);
public BanTime(id)
{
	Ban(id);
}

forward KickTime(id);
public KickTime(id)
{
	Kick(id);
}

forward dtext(playerid);
public dtext(playerid)
{
  	timer[playerid] = SetTimerEx("dtext", 1000, 0, "i", playerid);
    new Float:X, Float:Y, Float:Z;
    GetObjectPos(mine[playerid][0], X, Y, Z);
 	if(minecount[playerid] == 11)
    {
        minetext[playerid] = Create3DTextLabel("to activate the left: \n5 seconds!",0x00F900AA,X,Y,Z+1,40.0,0);
  		minecount[playerid] = 10;
 	}
	else if(minecount[playerid] == 10)
    {
        Update3DTextLabelText(minetext[playerid], 0x64F801AA, "Activation in: \n4 seconds!");
  		minecount[playerid] = 9;
 	}
	else if(minecount[playerid] == 9)
    {
        Update3DTextLabelText(minetext[playerid], 0xBAF801AA, "Activation in: \n3 seconds!");
  		minecount[playerid] = 8;
 	}
	else if(minecount[playerid] == 8)
    {
        Update3DTextLabelText(minetext[playerid], 0xDAF900AA, "Activation in: \n2 seconds!");
  		minecount[playerid] = 7;
 	}
 	else if(minecount[playerid] == 7)
    {
       	Update3DTextLabelText(minetext[playerid], 0xFFEB18AA, "Activation in: \n1 seconds!");
  		minecount[playerid] = 1;
 	}
 	else if(minecount[playerid] == 1)
 	{

        KillTimer(timer[playerid]);
        Delete3DTextLabel(minetext[playerid]);
        minecount[playerid] = 0;
        minePickup[playerid] = CreatePickup(0, 1, X, Y, Z, -1);
 	}
    return 1;
}

public ChangeMap()
{
		new string[64];
		tKills=0;
		ctKills=0;
		for(new i=0;i<18;i++) DestroyObject(Top3Objects[i]);
		for(new i=0;i<MAX_PLAYERS;i++)
		{
		    PlayerInfo[i][pRoundkills]=0;
    		DestroyPickup(minePickup[i]);
  			DestroyObject(mine[i][0]);
  			DestroyObject(mine[i][1]);
  			status[i] = 0;
  			SetCameraBehindPlayer(i);
		}
		format(string, sizeof(string), "RUS: %d", tKills);
		TextDrawSetString(TDtKills, string);
		TextDrawShowForAll(TDtKills);
		format(string, sizeof(string), "USA: %d", ctKills);
		TextDrawSetString(TDctKills, string);
		TextDrawShowForAll(TDctKills);
		format(string, sizeof(string), "unloadfs %s", LastFS);
		SendRconCommand(string);
		format(string, sizeof(string), "loadfs %s", MapInfo[MAP][mFscriptName]);
		SendRconCommand(string);
        TextDrawSetString(CurrentMap,MapInfo[MAP][mName]);
        SendRconCommand(MapInfo[MAP][mName]);
        format(LastFS, sizeof(LastFS), "%s", MapInfo[MAP][mFscriptName]);
		SendClientMessageToAll(-1,"SERVER: {F9E8B7}Map changed.");
        maptimer = SetTimer("Second",1000,true);
		timeleft = ROUND_MINUTES;
		TextDrawShowForAll(CurrentMap);
		RespawnAll();
        UnFreezeAll();
  		FirstBlood = 0;
		for(new i=0; i<MAX_PLAYERS; i++)
		{
		//	PlayAudioStreamForPlayer(i, "http://soundfxcenter.com/video-games/counter-strike/8e8118_Counter_Strike_Go_Go_Go_Sound_Effect.mp3");
        }
}

//==============================================================================
stock GetPlayersInTeamFromMaxPlayers(teamid)//Stock developed by Weponz (Weponz Inc. © 2010 - 2011)
{
    new playercount = 0;//Set our count to 0 as we have not counted any players yet..
    for(new i = 0; i < MAX_PLAYERS; i++)//Loop through MAX_PLAYERS(I suggest you redefine MAX_PLAYERS to ensure max efficency)..
    {
        if(GetPlayerState(i) == PLAYER_STATE_NONE) continue;//If a player is in class selection continue..
        if(gTeam[i] != teamid) continue;//If a player is NOT in the specified teamid continue..
        playercount++;//else (there in the teamid) so count the player in the team..
    }
    return playercount;//Return the total players counted in the specified team..
}
forward SendMSG();
public SendMSG()
{
    new randMSG = random(sizeof(RandomMSG));
    SendClientMessageToAll(COLOR_WHITE, RandomMSG[randMSG]);
}
forward LevelUpdating(i);
public LevelUpdating(i)
{
	if(PlayerInfo[i][pLevel] == 1)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Private 1st Class");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/8000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 2)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Private 2nd Class");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/18000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 3)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Private 3rd Class");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/29000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 4)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Lance Corporal 1 Star");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/41000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 5)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Lance Corporal 2 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/54000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 6)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Lance Corporal 3 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/67000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 7)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Corporal 1 Star");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/81000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 8)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Corporal 2 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/96000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 9)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Corporal 3 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/111000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 10)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Sergeant 1 Star");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/131000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 11)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Sergeant 2 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/150000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 12)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Sergeant 3 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/170000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 13)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Staff Sergeant 1 Star");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/190000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 14)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Staff Sergeant 2 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/220000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 15)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Staff Sergeant 3 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/250000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 16)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Gunnery Sergeant 1 Star");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/280000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 17)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Gunnery Sergeant 2 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/310000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 18)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Gunnery Sergeant 3 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/340000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 19)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Master Sergeant 1 Star");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/370000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 20)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Master Sergeant 2 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/400000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 21)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Master Sergeant 3 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/430000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 22)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"First Sergeant 1 Star");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/470000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 23)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"First Sergeant 2 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/510000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 24)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"First Sergeant 3 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/550000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 25)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Master Gunnery Sergeant");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/590000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 26)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Sergeant Major 1 Star");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/630000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 27)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Sergeant Major 2 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/670000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 28)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Sergeant Major 3 Stars");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/710000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 29)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Warrant Officer One");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/760000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 30)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Chief Warrant Officer Two");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/810000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 31)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Chief Warrant Officer Three");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/860000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 32)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Chief Warrant Officer Four");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/910000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 33)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Chief Warrant Officer Five");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/960000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 34)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Second Lieutenant");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/1010000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 35)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"First Lieutenant");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/1060000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 36)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Captain");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/1110000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 37)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Major");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/1165000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 38)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Lieutenant Colonel");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/1220000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 39)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"Colonel");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/1280000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}
	if(PlayerInfo[i][pLevel] == 40)
	{
		new rankstring[127];
		format(rankstring,sizeof(rankstring),"General");
		PlayerTextDrawSetString(i,Rank[i],rankstring);

		new zanivostring[128];
		format(zanivostring,sizeof(zanivostring),"/1340000");
		PlayerTextDrawSetString(i,zanivo[i],zanivostring);
	}

	if(PlayerInfo[i][pXP] >= 0)
	{
		PlayerInfo[i][pLevel] = 1;
	}
	if(PlayerInfo[i][pXP] >= 1000)
	{
	    PlayerInfo[i][pLevel] = 2;
 	}
	if(PlayerInfo[i][pXP] >= 8000)
	{
	    PlayerInfo[i][pLevel] = 3;
 	}
	if(PlayerInfo[i][pXP] >= 18000)
	{
	    PlayerInfo[i][pLevel] = 4;
 	}
	if(PlayerInfo[i][pXP] >= 29000)
	{
	    PlayerInfo[i][pLevel] = 5;
 	}
	if(PlayerInfo[i][pXP] >= 41000)
	{
	    PlayerInfo[i][pLevel] = 6;
 	}
	if(PlayerInfo[i][pXP] >= 54000)
	{
	    PlayerInfo[i][pLevel] = 7;
 	}
	if(PlayerInfo[i][pXP] >= 67000)
	{
	    PlayerInfo[i][pLevel] = 8;
 	}
	if(PlayerInfo[i][pXP] >= 81000)
	{
	    PlayerInfo[i][pLevel] = 9;
 	}
	if(PlayerInfo[i][pXP] >= 96000)
	{
	    PlayerInfo[i][pLevel] = 10;
 	}
	if(PlayerInfo[i][pXP] >= 111000)
	{
	    PlayerInfo[i][pLevel] = 11;
 	}
	if(PlayerInfo[i][pXP] >= 130000)
	{
	    PlayerInfo[i][pLevel] = 12;
 	}
	if(PlayerInfo[i][pXP] >= 150000)
	{
	    PlayerInfo[i][pLevel] = 13;
 	}
	if(PlayerInfo[i][pXP] >= 170000)
	{
	    PlayerInfo[i][pLevel] = 14;
 	}
	if(PlayerInfo[i][pXP] >= 190000)
	{
	    PlayerInfo[i][pLevel] = 15;
 	}
	if(PlayerInfo[i][pXP] >= 220000)
	{
	    PlayerInfo[i][pLevel] = 16;
 	}
	if(PlayerInfo[i][pXP] >= 250000)
	{
	    PlayerInfo[i][pLevel] = 17;
 	}
	if(PlayerInfo[i][pXP] >= 280000)
	{
	    PlayerInfo[i][pLevel] = 18;
 	}
	if(PlayerInfo[i][pXP] >= 310000)
	{
	    PlayerInfo[i][pLevel] = 19;
 	}
	if(PlayerInfo[i][pXP] >= 340000)
	{
	    PlayerInfo[i][pLevel] = 20;
 	}
	if(PlayerInfo[i][pXP] >= 370000)
	{
	    PlayerInfo[i][pLevel] = 21;
 	}
	if(PlayerInfo[i][pXP] >= 400000)
	{
	    PlayerInfo[i][pLevel] = 22;
 	}
	if(PlayerInfo[i][pXP] >= 430000)
	{
	    PlayerInfo[i][pLevel] = 23;
 	}
	if(PlayerInfo[i][pXP] >= 470000)
	{
	    PlayerInfo[i][pLevel] = 24;
 	}
	if(PlayerInfo[i][pXP] >= 510000)
	{
	    PlayerInfo[i][pLevel] = 25;
 	}
	if(PlayerInfo[i][pXP] >= 550000)
	{
	    PlayerInfo[i][pLevel] = 26;
 	}
	if(PlayerInfo[i][pXP] >= 590000)
	{
	    PlayerInfo[i][pLevel] = 27;
 	}
	if(PlayerInfo[i][pXP] >= 630000)
	{
	    PlayerInfo[i][pLevel] = 28;
 	}
	if(PlayerInfo[i][pXP] >= 670000)
	{
	    PlayerInfo[i][pLevel] = 29;
 	}
	if(PlayerInfo[i][pXP] >= 710000)
	{
	    PlayerInfo[i][pLevel] = 30;
 	}
	if(PlayerInfo[i][pXP] >= 760000)
	{
	    PlayerInfo[i][pLevel] = 31;
 	}
	if(PlayerInfo[i][pXP] >= 810000)
	{
	    PlayerInfo[i][pLevel] = 32;
 	}
	if(PlayerInfo[i][pXP] >= 860000)
	{
	    PlayerInfo[i][pLevel] = 33;
 	}
	if(PlayerInfo[i][pXP] >= 910000)
	{
	    PlayerInfo[i][pLevel] = 34;
 	}
	if(PlayerInfo[i][pXP] >= 960000)
	{
	    PlayerInfo[i][pLevel] = 35;
 	}
	if(PlayerInfo[i][pXP] >= 1010000)
	{
	    PlayerInfo[i][pLevel] = 36;
 	}
	if(PlayerInfo[i][pXP] >= 1060000)
	{
	    PlayerInfo[i][pLevel] = 37;
 	}
	if(PlayerInfo[i][pXP] >= 1110000)
	{
	    PlayerInfo[i][pLevel] = 38;
 	}
	if(PlayerInfo[i][pXP] >= 1165000)
	{
	    PlayerInfo[i][pLevel] = 39;
 	}
	if(PlayerInfo[i][pXP] >= 1220000)
	{
	    PlayerInfo[i][pLevel] = 40;
 	}

	new levelstring[127];
	format(levelstring,sizeof(levelstring),"Level: %d",PlayerInfo[i][pLevel]);
  	PlayerTextDrawSetString(i,Level[i],levelstring);

	new xpstring[128];
	format(xpstring,sizeof(xpstring),"XP: %d",PlayerInfo[i][pXP]);
  	PlayerTextDrawSetString(i,XP[i],xpstring);

	return 1;
}

stock SendAdminMessage(color,const string[])
{
    for(new i; i<GetMaxPlayers(); i++)
    {
        if(IsPlayerConnected(i))
        {
            SendClientMessage(i, color, string);
            printf("%s", string);
        }
    }
    return 1;
}
WasteDeAMXersTime()
{
    new b;
    #emit load.pri b
    #emit stor.pri b
}
AntiDeAMX()
{
    new a[][] =
    {
        "Unarmed (Fist)",
        "Brass K"
    };
    #pragma unused a
}
stock p_name( playerid )
{
	new p_namev[MAX_PLAYER_NAME];
	GetPlayerName(playerid, p_namev, MAX_PLAYER_NAME);
	return p_namev;
}
stock ShowCustomClass(playerid)
{
	//PLAYER AND CAMERA
	TogglePlayerControllable(playerid,false);
	TextDrawShowForPlayer(playerid,Selection_TD[0]);
	TextDrawShowForPlayer(playerid,Selection_TD[1]);
	TextDrawShowForPlayer(playerid,Selection_TD[2]);
	TextDrawShowForPlayer(playerid,Selection_TD[3]);
	TextDrawShowForPlayer(playerid,Selection_TD[4]);
	TextDrawShowForPlayer(playerid,Selection_TD[5]);
	TextDrawShowForPlayer(playerid,Selection_TD[6]);
	TextDrawShowForPlayer(playerid,Selection_TD[7]);
	TextDrawShowForPlayer(playerid,Selection_TD[8]);
	TextDrawShowForPlayer(playerid,Selection_TD[9]);
	TextDrawShowForPlayer(playerid,Selection_TD[10]);
	TextDrawShowForPlayer(playerid,Selection_TD[11]);
	TextDrawShowForPlayer(playerid,Selection_TD[12]);
	TextDrawShowForPlayer(playerid,Selection_TD[13]);
	TextDrawShowForPlayer(playerid,Selection_TD[14]);
	TextDrawShowForPlayer(playerid,Selection_TD[15]);
	TextDrawShowForPlayer(playerid,Selection_TD[16]);
	TextDrawShowForPlayer(playerid,Selection_TD[17]);
	TextDrawShowForPlayer(playerid,Selection_TD[18]);
	TextDrawShowForPlayer(playerid,Selection_TD[19]);
	TextDrawShowForPlayer(playerid,Selection_TD[20]);
	TextDrawShowForPlayer(playerid,Selection_TD[21]);
	TextDrawShowForPlayer(playerid,Selection_TD[22]);
	TextDrawShowForPlayer(playerid,Selection_TD[23]);
	SelectTextDraw(playerid, 0xA3B4C5FF);
}
public warned1(playerid) {
	if (warned[playerid] == 1) {
	    warned[playerid] = 1;
	}
	if (warned[playerid] == 2) {
	    warned[playerid] = 2;
	}
	if (warned[playerid] == 3) {
	    warned[playerid] = 3;
	}
    return 1;
}
forward TimeOnServer(playerid);
public TimeOnServer(playerid)
{
PlayerInfo[playerid][pSec] ++;
if(PlayerInfo[playerid][pSec]>=60)
	{
		PlayerInfo[playerid][pMin]++;
  		PlayerInfo[playerid][pSec]=0;
	}
if(PlayerInfo[playerid][pMin]>=60)
	{
		PlayerInfo[playerid][pMin]=0;
		PlayerInfo[playerid][pHour]++;
	}
}
stock SendClientMessageToAdmins(message[])
{
    loop(i, MAX_PLAYERS) {
        if (IsPlayerConnected(i)) {
            if (PlayerInfo[i][pAdmin] >= 1) {
                SendClientMessage(i, COLOR_ORANGE, message);
			}
		}
    }
    return 1;
}

forward MessageToAdmins(color,const string[]);
public MessageToAdmins(color,const string[])
{
	for(new i = 0; i < MAX_PLAYERS; i++)
	{
	if(IsPlayerConnected(i) == 1)
	if(PlayerInfo[i][pAdmin] >= 1)
	SendClientMessage(i, color, string);
	}
	return 1;
}
// /Report Cmd
stock ReportPlayer(playerid, reporter, reason[]) {
	new PlayerName[MAX_PLAYER_NAME];
	GetPlayerName(reporter, PlayerName, sizeof(PlayerName));
	new szName[MAX_PLAYER_NAME];
	GetPlayerName(playerid, szName, sizeof(szName));
    format(szString, sizeof(szString), "[REPORT] %s  (ID:%d) reported %s (ID:%d) for %s", PlayerName, reporter, szName, playerid, reason);
    SendClientMessageToAdmins(szString);
    return 1;
}
stock SendClientMessageToNotify(message[]) {
    loop(i, MAX_PLAYERS) {
        if (IsPlayerConnected(i)) {
            if (PlayerInfo[i][pAdmin] >= 2) {
                if(Notify[i] == 1) {
     				SendClientMessage(i, COLOR_RED, message);
				}
			}
		}
	}
	return 1;
}
stock StartFPS(playerid) //To start the FPS mod.
{
    FirstPersonObject[playerid] = CreateObject(19300, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0);//You must need this, if you don't the camera will glitch.
    AttachObjectToPlayer(FirstPersonObject[playerid],playerid, 0.0, 0.12, 0.7, 0.0, 0.0, 0.0);//Just Attached the object.
    AttachCameraToObject(playerid, FirstPersonObject[playerid]);//Now, I have sttach my camera to that object that i have created.
    FirstPerson[playerid] = 1;//New variable is now TRUE ( 1= True)
    CallLocalFunction("OnPlayerHaveFirstPerson", "i", playerid);//I have called the Local Function of OnPlayerHaveFirstPerson .
    return 1;
}

stock StopFPS(playerid) //As you have start the FPS, That must need deactive function too .
{
    SetCameraBehindPlayer(playerid);// Just moved his camera behind the player.
    DestroyObject(FirstPersonObject[playerid]);//Destroyed that Object that i have created on first.
    FirstPerson[playerid] = 0;// 0= False .
    CallLocalFunction("OnPlayerDoNotHaveFirstPerson", "i", playerid);//Same as before, I have just deactive that callback ( called OnPlayerDoNotHaveFirstPerson .
    return 1;
}

stock Reset(playerid)
{
    FirstPerson[playerid] = 0;//Just if you are bugged . Reset.
}

forward TeamChat(playerid,string[]);

public TeamChat(playerid,string[])
{
    for(new i = 0; i < MAX_PLAYERS; i++)
    {
        if(GetPlayerTeam(i) == GetPlayerTeam(playerid))
        {
            SendClientMessage(i,-1,string);
        }
    }
    return 1;
}


forward REGENHP(playerid);
public REGENHP(playerid)
{
        if(Damaged[playerid] == 0 && Dead[playerid] == 0 && Spawned[playerid] == 1)
        {
                if(Dead[playerid] == 1) return 1;
                if(HP(playerid) < 100) SetPlayerHealth(playerid,HP(playerid)+1);
                SetTimerEx("REGENHP",100,0,"i",playerid);
        }
        else SetTimerEx("REGENHP",100,0,"i",playerid);
        return 1;
}
//obrisi
forward DamageReset(playerid);
public DamageReset(playerid)
{
    hit[playerid] = 0;
    damage[playerid] = 0;
    PlayerTextDrawHide(playerid, IndicatorBox[playerid]);
    PlayerTextDrawHide(playerid, Indicator[playerid]);
    KillTimer(timerdamage[playerid]);
    return 1;
}
forward UNDMG(playerid);
public UNDMG(playerid)
{
	Damaged[playerid] = 0;
	return 1;
}

stock HP(playerid)
{
        new Float:health;
        GetPlayerHealth(playerid, health);
        new hp = floatround( health );
        return hp;
}

stock FreezeAll()
{
        for(new i=0; i<MAX_PLAYERS; i++)
        {
            TogglePlayerControllable(i,0);
        }
}

stock UnFreezeAll()
{
        for(new i=0; i<MAX_PLAYERS; i++)
        {
            TogglePlayerControllable(i,1);
        }
}

stock RespawnAll()
{
        for(new i=0; i<MAX_PLAYERS; i++)
        {
            TogglePlayerSpectating(i,1);
            SPAWNED[i] = false;
            TogglePlayerSpectating(i,0);
        }
}

stock IsPlayerAiming(playerid)
{
	new anim = GetPlayerAnimationIndex(playerid);
	if (((anim >= 1160) && (anim <= 1163)) || (anim == 1167) || (anim == 1365) || (anim == 1643) || (anim == 1453) || (anim == 220)) return 1;
    return 0;
}

stock AddMap(Name[], FscriptName[], Float:TspawnX, Float:TspawnY, Float:TspawnZ, Float:CTspawnX, Float:CTspawnY, Float:CTspawnZ)
{
	format(MapInfo[mapid][mName], sizeof(MapInfo[]),"%s", Name);
	format(MapInfo[mapid][mFscriptName], sizeof(MapInfo[]),"%s", FscriptName);
	MapInfo[mapid][mCTSpawnX]=CTspawnX;
	MapInfo[mapid][mCTSpawnY]=CTspawnY;
	MapInfo[mapid][mCTSpawnZ]=CTspawnZ;
	MapInfo[mapid][mTSpawnX]=TspawnX;
	MapInfo[mapid][mTSpawnY]=TspawnY;
	MapInfo[mapid][mTSpawnZ]=TspawnZ;
	mapid++;
}

public OnDialogResponse(playerid, dialogid, response, listitem, inputtext[])
{
    switch( dialogid )
    {
        case DIALOG_REGISTER:
        {
            if (!response) return Kick(playerid);
            if(response)
            {

                if(!strlen(inputtext)) return ShowPlayerDialog(playerid, DIALOG_REGISTER, DIALOG_STYLE_PASSWORD, ""COL_WHITE"Registering...",""COL_RED"You have entered an invalid password.\n"COL_WHITE"Type your password below to register a new account.","Register","Quit");
                new INI:File = INI_Open(UserPath(playerid));
                INI_SetTag(File,"data");
                INI_WriteInt(File,"Password",udb_hash(inputtext));
                INI_WriteInt(File,"Admin",0);
                INI_WriteInt(File,"Kills",0);
                INI_WriteInt(File,"Deaths",0);
                INI_WriteInt(File,"Banned", 0);
                INI_WriteInt(File,"Level", 1);
                INI_WriteString(File, "Rank", "Newbie");
                INI_WriteInt(File,"XP",0);
                new time[64],date[64],h,mi,s,d,m,y;
                gettime(h,mi,s);
                getdate(d,m,y);
                format(time,sizeof(time),"%02d/%02d/%02d",h,mi,s);
                format(date,sizeof(date),"%02d/%02d/%d",d,m,y);
                INI_WriteString(File,"RegisterTime",time);
                INI_WriteString(File,"RegisterDate",date);
                PlayerInfo[playerid][pLevel] = 1;
                PlayerInfo[playerid][pAdmin] = 0;
                INI_Close(File);
                SendClientMessage(playerid, COLOR_GREEN, "Great! You have successfully registered.");
            }
        }
        case DIALOG_LOGIN:
        {
            if ( !response ) return Kick ( playerid );
            if( response )
            {
                if(udb_hash(inputtext) == PlayerInfo[playerid][pPass])
                {
                    INI_ParseFile(UserPath(playerid), "LoadUser_%s", .bExtra = true, .extra = playerid);
					SetPlayerScore(playerid, PlayerInfo[playerid][pLevel]);
                    SendClientMessage(playerid, COLOR_GREEN, "Great! You have successfully logged in.");
				}
                else
                {
                    ShowPlayerDialog(playerid, DIALOG_LOGIN, DIALOG_STYLE_PASSWORD,""COL_WHITE"Login",""COL_RED"You have entered an incorrect password.\n"COL_WHITE"Type your password below to login.","Login","Quit");
                }
                return 1;
            }
        }
    }
    if(dialogid == 22)
    {
		if(response)
		{
		    SendClientMessage(playerid,COLOR_ORANGE,"We hope that helped you!");
		}
	}
 	if(dialogid == 23)
    {
		if(response)
		{
		    SendClientMessage(playerid,COLOR_ORANGE,"We hope that helped you!");
		}
	}
    return 1;
}

//==============================ADMIN=SETTINGS================================//
CMD:setadmin(playerid, params[])
{
	new id,level,msg[128],msg2[128],name[MAX_PLAYER_NAME];
	if(IsPlayerAdmin(playerid))
	{
		if(sscanf(params,"ud",id,level)) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}USAGE{FFFFFF}]: /setadmin [name/id] [level]");
		if (level > 5) return SendClientMessage(playerid,COLOR_RED,"Maximum level is 5");
		//F7B62A
		PlayerInfo[id][pAdmin] = level;
		format(msg,sizeof(msg), "Congratulations , now you are admin level %d",level);
		SendClientMessage(id,COLOR_GREEN,msg);
		GetPlayerName(id,name,sizeof(name));
		format(msg2,sizeof(msg2),"You've set admin to %s , level %d !",name,level);
		SendClientMessage(playerid,COLOR_GREEN,msg2);
 	}
 	else
 	{
			if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
	}
	return 1;
}

//-------------------------LEVEL 1 COMMANDS-----------------------------------//
CMD:jetpack(playerid, params[])
{
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
	if(PlayerInfo[playerid][pAdmin] < 5) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]:You need to be admin level 5 to use this command!");
	SetPlayerSpecialAction(playerid, SPECIAL_ACTION_USEJETPACK);
	return 1;
}

CMD:ban(playerid,params[])
{
	new id,reason[128],msg[128],name[MAX_PLAYER_NAME];
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
	if(sscanf(params,"us[128]", id, reason)) return SendClientMessage(playerid, COLOR_WHITE,"[{F7B62A}USAGE{FFFFFF}]: /ban [id] [reason]");
	if(IsPlayerConnected(id)) return
	GetPlayerName(id,name,sizeof(name));
	format(msg,sizeof(msg),"%s has been banned from the server! Reason : % s",name,reason);
	SendClientMessageToAll(COLOR_ORANGE,msg);
	PlayerInfo [id] [pBanned] = 1;
	TogglePlayerControllable(id,0);
	ResetPlayerWeapons(id);
	SetPlayerInterior(id,10);
	SetPlayerPos(id, 366.9682,-57.3005,1001.5103);
	SetPlayerCameraPos(id, 366.8701,-61.6168,1002.5078);
	SetPlayerCameraLookAt(id, 366.9682,-57.3005,1001.5103);
	SetPlayerFacingAngle(id, 180 );
	ApplyAnimation(playerid, "ON_LOOKERS", "wave_loop", 4.0, 1, 0, 0, 0, 0); // Wave
	SetTimerEx("BanTime", 500, false, "i", id);
	return 1;
}

CMD:ann(playerid,params[])
{
	new msg[60];
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
	if(sscanf(params,"s[60]",msg)) return SendClientMessage(playerid, COLOR_WHITE,"[{F7B62A}USAGE{FFFFFF}]: /ann [message]");
 	GameTextForAll(msg,5000,3);
	return 1;
}

CMD:kick(playerid,params[])
{
	new id,reason[128],msg[128],msg2[128],name[MAX_PLAYER_NAME];
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
	if(sscanf(params,"us[128]",id,reason)) return SendClientMessage(playerid,COLOR_WHITE,"[{F7B62A}USAGE{FFFFFF}]: /kick [id] [reason]");
    GetPlayerName(id,name,sizeof(name));
    format(msg,sizeof(msg),"[%s(%d) has been kicked from the server],[{F7B62A}Reason{FFFFFF}]: %s",name,id,reason);
    SendClientMessageToAll(COLOR_ORANGE,msg);
    format(msg2,sizeof(msg2),"[{F7B62A}You have been kicked from server{FFFFFF}],[{F7B62A}Reason{FFFFFF}]: %s",reason);
    SendClientMessage(id,COLOR_WHITE,msg2);
	TogglePlayerControllable(id,0);
	ResetPlayerWeapons(id);
	SetPlayerInterior(id,10);
	SetPlayerPos(id, 366.9682,-57.3005,1001.5103);
	SetPlayerCameraPos(id, 366.8701,-61.6168,1002.5078);
	SetPlayerCameraLookAt(id, 366.9682,-57.3005,1001.5103);
	SetPlayerFacingAngle(id, 180 );
	ApplyAnimation(id, "ON_LOOKERS", "wave_loop", 4.0, 1, 0, 0, 0, 0); // Wave
	SetTimerEx("KickTime", 500, false, "i", id);
	return 1;
}

CMD:slap(playerid,params[])
{
	new id,Float:x,Float:z,Float:y,name[MAX_PLAYER_NAME],msg[128];
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
	if(sscanf(params,"u",id)) return SendClientMessage(playerid,COLOR_WHITE,"[{F7B62A}USAGE{FFFFFF}]: /slap [id]");
	if(PlayerInfo[playerid][pAdmin] <= 1) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]:You need to be admin level 2 to use this command!");
	if(id == INVALID_PLAYER_ID) return SendClientMessage(playerid,COLOR_RED,"Wrong player id!");
   	GetPlayerPos(id,x,y,z);
   	SetPlayerPos(id,x,y,z+4);
   	GetPlayerName(playerid,name,sizeof(name));
    format(msg,sizeof(msg),"You have been slapped by admin %s!",name);
	SendClientMessage(id,COLOR_RED,msg);
	return 1;
}

CMD:explode(playerid,params[])
{
    new id,Float:x,Float:z,Float:y,name[MAX_PLAYER_NAME],msg[128];
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
	if(sscanf(params,"u",id)) return SendClientMessage(playerid,COLOR_WHITE,"[{F7B62A}USAGE{FFFFFF}]: /explode [id]");
	if(PlayerInfo[playerid][pAdmin] <= 1) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]:You need to be admin level 2 to use this command!");
	if(id == INVALID_PLAYER_ID) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]: Wrong player id!");
	GetPlayerPos(id,x,y,z);
	CreateExplosion(x,y,z,12,10.0);
	GetPlayerName(id,name,sizeof(name));
	format(msg,sizeof(msg),"[{F7B62A}You've explode %s(%d){FFFFFF}]",name,id);
	SendClientMessage(playerid,COLOR_WHITE,msg);
	return 1;
}

CMD:akill(playerid,params[])
{
    new id, name[MAX_PLAYER_NAME],msg[128];
	if(PlayerInfo[playerid][pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
	if(sscanf(params,"u",id)) return SendClientMessage(playerid,COLOR_WHITE,"[{F7B62A}USAGE{FFFFFF}]: /explode [id]");
	if(id == INVALID_PLAYER_ID) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]: Wrong player id!");
	GetPlayerName(id,name,sizeof(name));
	format(msg,sizeof(msg),"[{F7B62A}You've killed %s(%d){FFFFFF}]",name,id);
	SendClientMessage(playerid,COLOR_WHITE,msg);
	SendClientMessage(id, COLOR_WHITE, "An admin has killed you.");
	SetPlayerHealth(id,0);
	return 1;
}

CMD:allahuakbar(playerid,params[])
{
	new Float:x,Float:y,Float:z;
	GetPlayerPos(playerid,x,y,z);
	CreateExplosion(x,y,z,12,10.0);
	SetPlayerHealth(playerid,0.0);
	PlayAudioStreamForPlayer(playerid,"http://k003.kiwi6.com/hotlink/5gpl2ybm4g/Allahu_Akbar_Sound_Effect_mp3cut.net_.mp3");
}

CMD:ivan(playerid,params[])
{
	SetPlayerAttachedObject(playerid,0,343,1,-0.303999,0.125999,0.214999,-4.699998,84.200004,0.599999,1.000000,1.000000,1.000000);
	SetPlayerAttachedObject(playerid,1,355,1,-0.277000,-0.131000,0.000000,10.200011,25.100006,0.000000,1.000000,1.000000,1.000000);
    SetPlayerAttachedObject(playerid,2,349,1,-0.319000,-0.111999,-0.112000,-7.799999,-21.100002,-2.400000,1.000000,1.000000,1.000000);
    SetPlayerAttachedObject(playerid,3,353,1,0.113999,-0.129000,0.047999,-4.699997,39.699996,7.499999,1.000000,1.000000,1.000000);
    SetPlayerAttachedObject(playerid,4,351,5,50.036000,0.090999,0.040999,142.999969,-66.300018,-5.900000,1.000000,1.000000,1.000000);
    SetPlayerAttachedObject(playerid,5,361,6,0.015999,-0.025000,-0.037000,-0.699999,-68.999977,0.000000,1.000000,1.000000,1.000000);
    SetPlayerAttachedObject(playerid,6,19515,1,0.120999,0.079999,0.000000,0.000000,0.000000,0.000000,1.000000,1.000000,1.000000);
    SetPlayerAttachedObject(playerid,7,346,1,-0.219999,0.158999,-0.264999,134.200012,86.299980,0.000000,1.000000,1.000000,1.000000);
    SetPlayerAttachedObject(playerid,8,358,1,-0.234999,-0.104000,-0.043000,0.000000,0.000000,0.000000,1.000000,1.000000,1.000000);
    SetPlayerAttachedObject(playerid,9,334,7,0.481999,0.044999,0.107999,-100.099967,-70.699951,42.999988,1.000000,1.000000,1.000000);
    PlayAudioStreamForPlayer(playerid,"http://k003.kiwi6.com/hotlink/p0o1ogljn3/Red_Army_Choir-_Kalinka._mp3cut.net_.mp3");
	return 1;
}

CMD:goto(playerid,params[])
{
	new id,Float:x,Float:z,Float:y,name[MAX_PLAYER_NAME],msg[128];
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
	if(sscanf(params,"u",id)) return SendClientMessage(playerid,COLOR_WHITE,"[{F7B62A}USAGE{FFFFFF}]: /goto [id]");
	if(PlayerInfo[playerid][pAdmin] < 3) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]:You need to be admin level 3 to use this command!");
	if(id == INVALID_PLAYER_ID) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]: Wrong player id!");
	if(id == playerid) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]: You can't teleport to yourself!");
    GetPlayerPos(id,x,y,z);
    SetPlayerPos(playerid,x+1,y+1,z);
    GetPlayerName(id,name,sizeof(name));
    format(msg,sizeof(msg),"[{F7B62A}You have been teleported to %s(%d){FFFFFF}]",name,id);
    SendClientMessage(playerid,COLOR_WHITE,msg);
 	return 1;
}

CMD:get(playerid,params[])
{
	new id,Float:x,Float:z,Float:y,name[MAX_PLAYER_NAME],msg[128];
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
	if(sscanf(params,"u",id)) return SendClientMessage(playerid,COLOR_WHITE,"[{F7B62A}USAGE{FFFFFF}]: /get [id]");
	if(PlayerInfo[playerid][pAdmin] < 3) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]:You need to be admin level 3 to use this command!");
	if(id == INVALID_PLAYER_ID) return SendClientMessage(playerid,COLOR_RED,"Wrong player id!");
	if(id == playerid) return SendClientMessage(playerid,COLOR_RED,"You can't teleport to yourself!");
    GetPlayerPos(playerid,x,y,z);
    SetPlayerPos(id,x+1,y+1,z);
    GetPlayerName(id,name,sizeof(name));
    format(msg,sizeof(msg),"[{F7B62A}You've teleported %s(%d)to yourself {FFFFFF}]",name,id);
    SendClientMessage(playerid,COLOR_WHITE,msg);
 	return 1;
}

CMD:cc(playerid,params[])
{
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
	for(new i = 0; i < 99; i++) SendClientMessageToAll(0x00000000," ");
    SendClientMessageToAll(COLOR_WHITE,"[{F7B62A}CHAT HAS BEEN CLEARED BY ADMIN{FFFFFF}]");//TESTING
    return 1;
}

CMD:freeze(playerid,params[])
{
	new id;
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
    if(PlayerInfo[playerid][pAdmin] < 3) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]:You need to be admin level 3 to use this command!");
    if(sscanf(params,"u",id)) return SendClientMessage(playerid,COLOR_WHITE,"[{F7B62A}USAGE{FFFFFF}]: /freeze [playerid]");
    if(!IsPlayerConnected(id)) return SendClientMessage(playerid,0xFF0000AA,"[{F7B62A}Error{FFFFFF}]: Wrong player ID!");
    TogglePlayerControllable(id,0);
    SendClientMessage(id,COLOR_RED,"You are frozen by admin! ");
    return 1;
}

CMD:unfreeze(playerid,params[])
{
	new id;
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
    if(PlayerInfo[playerid][pAdmin] < 3) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]:You need to be admin level 3 to use this command!");
    if(sscanf(params,"u",id)) return SendClientMessage(playerid,COLOR_WHITE,"[{F7B62A}USAGE{FFFFFF}]: /freeze [playerid]");
    if(!IsPlayerConnected(id)) return SendClientMessage(playerid,0xFF0000AA,"[{F7B62A}Error{FFFFFF}]: Wrong player ID!");
    TogglePlayerControllable(id,1);
    SendClientMessage(id,COLOR_RED,"You are unfrozen by admin! ");
    return 1;
 }

CMD:asay(playerid, params[])
{
	new string[128];
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
  	format(string, sizeof(string), "[MAIN CHAT]: {00F525}Admin {FFFFFF}:{00F525} %s",params);
   	SendClientMessageToAll(COLOR_WHITE, string);
    return 1;
}
// PROBNE KOMANDEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE//
CMD:report(playerid, params[]) {
	new id;
    if(sscanf(params, "is[254]", id, params)) return SendClientMessage(playerid, COLOR_WHITE, "[{0xFF9900AA}USAGE{FFFFFF}]: /report <playerid> <reason>");
	if (!IsPlayerConnected(id)) return SendClientMessage(playerid, COLOR_RED, "ERROR: Player is not connected.");
    ReportPlayer(id, playerid, params);
    format(szString, sizeof(szString), "{FFFFFF}[{F7B62A}REPORT{FFFFFF}]: %s", params);
    SendClientMessage(playerid, COLOR_GREEN, szString);
	return 1;
}

CMD:warn(playerid,params[])
{
	new id,reason[100],str[128];
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
	if(sscanf(params,"ds",id,reason)) return SendClientMessage(playerid, COLOR_WHITE, "[{F7B62A}USAGE{FFFFFF}]: /warn <playerid> <reason>");
    if (!IsPlayerConnected(id)) return SendClientMessage(playerid, COLOR_RED, "ERROR: Player is not connected.");
	if(PlayerInfo[id][pAdmin] > PlayerInfo[playerid][pAdmin])return SendClientMessage(playerid,COLOR_RED,"ERROR: He/She is a greater level than your level.");
	GetPlayerName(id,str,MAX_PLAYER_NAME);
	if(warned[id] == 2)
	{
		new plrIP[64];
		GetPlayerIp(id, plrIP, 64);
		new Year, Month, Day;
		getdate(Year, Month, Day);
		format(szString, sizeof(szString), "*{F7B62A} %s (ID:%d) {FFFFFF}has been kicked for: {0xFF9900AA}Warning: {FFFFFF}[{0xFF9900AA}3/3{FFFFFF}]", str, id);
		SendClientMessageToAll(0xFF444499, szString);
		TogglePlayerControllable(id,0);
		ResetPlayerWeapons(id);
		SetPlayerInterior(id,10);
		SetPlayerPos(id, 366.9682,-57.3005,1001.5103);
		SetPlayerCameraPos(id, 366.8701,-61.6168,1002.5078);
		SetPlayerCameraLookAt(id, 366.9682,-57.3005,1001.5103);
		SetPlayerFacingAngle(id, 180 );
		ApplyAnimation(id,"BEACH", "ParkSit_M_loop", 4.0, 1, 0, 0, 0, 0);
		SendClientMessage(id, 0xF74646FF, "You have been kicked, please write the following details down..");
		format(szString, sizeof(szString), "* Admin: Admin System");
		SendClientMessage(id, 0x00B359FF, szString);
		format(szString, sizeof(szString), "* Reason: You received 3/3 warnings.", reason);
		Kick(id);
	}
	else
	{
		warned[id] ++;
		format(str, sizeof(str), "{F7B62A}%s (ID:%d) {FFFFFF}has been warned. [{F7B62A}%d/3{FFFFFF}]; Reason: {F7B62A}%s", str, id, warned[id], reason);
		SendClientMessageToAll(COLOR_RED, str);
	}
	return 1;
}

CMD:ip(playerid, params[])
{
	new id;
	if(PlayerInfo [playerid] [pAdmin] == 0 && !IsPlayerAdmin(playerid)) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
    if(sscanf(params, "i",id)) return SendClientMessage(playerid, COLOR_WHITE,"[{F7B62A}USAGE{FFFFFF}]: /ip <playerid>");
    if (!IsPlayerConnected(id)) return SendClientMessage(playerid, COLOR_RED, "ERROR: Player is not connected.");
	new PlayerName[MAX_PLAYER_NAME];
	GetPlayerName(playerid, PlayerName, sizeof(PlayerName));
	new GPlayerName[MAX_PLAYER_NAME];
	GetPlayerName(id, GPlayerName, sizeof(GPlayerName));
   	new pIP[16];
   	GetPlayerIp(id, pIP, 16);
   	format(szString, sizeof(szString), "* %s (ID:%d)'s IP: %s", GPlayerName, id, pIP);
   	SendClientMessage(playerid, COLOR_GREEN, szString);
    return 1;
}

CMD:nextmap(playerid,params[])
{
	if(PlayerInfo [playerid] [pAdmin] == 0) return SendClientMessage(playerid, COLOR_WHITE, "Unknown command,please use {F7B62A}/help{FFFFFF},{F7B62A}/cmds");
    if (PlayerInfo[playerid][pAdmin] < 5 && !IsPlayerAdmin(playerid)) return SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]:You need to be admin level 5 to use this command!");
	seconds=0;
	timeleft=0;
	CallLocalFunction("Second", "");
    return 1;
}

CMD:stats(playerid,params[])
{
	new name[MAX_PLAYER_NAME];
	new id;
	if(sscanf(params,"d",id)) return SendClientMessage(playerid, COLOR_RED, "USAGE: /stats <ID>");
	if (!IsPlayerConnected(id)) return SendClientMessage(playerid, COLOR_RED, "ERROR: Player is not connected.");
	new string[256];
	GetPlayerName(id, name, sizeof(name));
	new Float:ratio=floatdiv(PlayerInfo[id][pKills], PlayerInfo[id][pDeaths]);
	format(string, 256, "%d %d %.2f", PlayerInfo[id][pKills], PlayerInfo[id][pDeaths], ratio);
	format(string, sizeof(string),"Name: %s (ID:%d)\nKills : %d\nDeaths : %d\nKill/Death Ratio: %.2f \nTime Played: %d Hours, %d Minutes, %d Seconds\nRegister Date: %d ,Time %d\nLevel: %d\nXP: %d",name, id, PlayerInfo[id][pKills], PlayerInfo[id][pDeaths], ratio,PlayerInfo[id][pHour],PlayerInfo[id][pMin],PlayerInfo[id][pSec],PlayerInfo[id][RegisterDate],PlayerInfo[id][RegisterTime],PlayerInfo[id][pLevel],PlayerInfo[id][pXP]);
    ShowPlayerDialog(playerid,DIALOG_STATS,DIALOG_STYLE_MSGBOX,"Player Stats",string,"Close","");
    return 1;
}

CMD:setlevel10(playerid,params[])
{
	PlayerInfo[playerid][pLevel] = 10;
	return 1;
}

CMD:sel(playerid,params[])
{
   		TextDrawShowForPlayer(playerid,Selection_TD[0]);
		TextDrawShowForPlayer(playerid,Selection_TD[1]);
		TextDrawShowForPlayer(playerid,Selection_TD[2]);
		TextDrawShowForPlayer(playerid,Selection_TD[3]);
		TextDrawShowForPlayer(playerid,Selection_TD[4]);
		TextDrawShowForPlayer(playerid,Selection_TD[5]);
		TextDrawShowForPlayer(playerid,Selection_TD[6]);
		TextDrawShowForPlayer(playerid,Selection_TD[7]);
		TextDrawShowForPlayer(playerid,Selection_TD[8]);
		TextDrawShowForPlayer(playerid,Selection_TD[9]);
		TextDrawShowForPlayer(playerid,Selection_TD[10]);
		TextDrawShowForPlayer(playerid,Selection_TD[11]);
		TextDrawShowForPlayer(playerid,Selection_TD[12]);
		TextDrawShowForPlayer(playerid,Selection_TD[13]);
		TextDrawShowForPlayer(playerid,Selection_TD[14]);
		TextDrawShowForPlayer(playerid,Selection_TD[15]);
		TextDrawShowForPlayer(playerid,Selection_TD[16]);
		TextDrawShowForPlayer(playerid,Selection_TD[17]);
		TextDrawShowForPlayer(playerid,Selection_TD[18]);
		TextDrawShowForPlayer(playerid,Selection_TD[19]);
		TextDrawShowForPlayer(playerid,Selection_TD[20]);
		TextDrawShowForPlayer(playerid,Selection_TD[21]);
		TextDrawShowForPlayer(playerid,Selection_TD[22]);
		TextDrawShowForPlayer(playerid,Selection_TD[23]);
		SelectTextDraw(playerid, 0xA3B4C5FF);
		return 1;
}

CMD:top3(playerid,params[])
{
	SetPlayerPos(playerid,1451.1000000,-967.9000200,51.8000000);
	return 1;
}

CMD:menucam(playerid,params[])
{
	SetPlayerPos(playerid,365.5000000,379.7999900,61.2000000);
	return 1;
}

CMD:crveno(playerid,params[])
{
	TextDrawColor(Timeleft, COLOR_RED);
 	return 1;
}

CMD:belo(playerid,params[])
{
	TextDrawColor(Timeleft, COLOR_WHITE);
 	return 1;
}

CMD:setrank1(playerid,params[])
{
	new rankstring2[127];
	format(rankstring2,sizeof(rankstring2),"Private");
	PlayerTextDrawSetString(playerid,Rank[playerid],rankstring2);
	return 1;
}

CMD:setrank2(playerid,params[])
{
	new rankstring3[127];
	format(rankstring3,sizeof(rankstring3),"Private First asd");
	PlayerTextDrawSetString(playerid,Rank[playerid],rankstring3);
	return 1;
}

CMD:setlevel1(playerid,params[])
{
	PlayerInfo[playerid][pLevel] = 1;
	return 1;
}

CMD:setxp(playerid,params[])
{
	PlayerInfo[playerid][pXP] = 100000;
	return 1;
}

CMD:rules(playerid,params[])
{
	new string[512];
	format(string, sizeof(string),"1. No hacking/cheating by using any unfair program/mod\n2. No disrespecting other players and admins\n3. No bug abusing\n4. No arguing with admins or players\n5. No spamming the chat\n 6. No spawn killing\n7. Dont use the main chat to report a player,usa /report\n8. No avoiding death by pausing\n9. No advertising other server or any website\n10. No racism or nazism");
	ShowPlayerDialog(playerid,DIALOG_RULES,DIALOG_STYLE_MSGBOX,"Rules:",string,"Ok","");
	return 1;
}

CMD:cmd(playerid,params[])
{
	new string [256];
	format(string,sizeof(string),"/class - Change your class/weapons\n/team - Change your team\n/report - Report a player to admins online\n/help - Show help dialog\n/rules - Show rules dialog\n/stats - Show player stats\n/t - Teamchat\n/update(s) - See what's new on our server");
	ShowPlayerDialog(playerid,DIALOG_COMMAND,DIALOG_STYLE_MSGBOX,"Command:",string,"Nice","");
	return 1;
}

CMD:cmds(playerid,params[])
{
	return cmd_cmd(playerid,params);
}

CMD:a(playerid,params[])
{
	if(PlayerInfo[playerid][pAdmin] == 0) return SendClientMessage(playerid,COLOR_WHITE,"[{F7B62A}ERROR{FFFFFF}]:You need to be admin to use this command!");
	if(PlayerInfo[playerid][pAdmin] >=1)
	{
		new text[128],pName[MAX_PLAYER_NAME],formatted[156];
		if(sscanf(params,"s[128]",text)) return SendClientMessage(playerid,-1,"Usage: /a [text]");
		GetPlayerName(playerid,pName,sizeof(pName));
		format(formatted,sizeof(formatted),"[{00F525}ADMIN CHAT{FFFFFF}]: {00F525}%s {FFFFFF}: %s",pName,text);
		MessageToAdmins(COLOR_WHITE,formatted);
	}
	return 1;
}

CMD:updates(playerid,params[])
{
	return cmd_update(playerid,params);
}

CMD:update(playerid,params[])
{
	ShowPlayerDialog(playerid,15,DIALOG_STYLE_MSGBOX ,"[TDM] Battlefield 0.3.7 Updates","{FFFFFF}Latest update on {{4DC1E8}}29.9.2014, 22:28{FFFFFF} v0.7.\n{00FF00}[ADDED]{FFFFFF} New HUD!\n{00FF00}[ADDED]{FFFFFF} New fully working Level / Rank / XP system!","Nice!","");
	//\n{FF0000}[REMOVED]{FFFFFF}   Destruction map.
	return 1;
}

CMD:mine(playerid,params[])
{
	if(status[playerid] == 1)
	{
		SendClientMessage(playerid, WHITE, " You have already established a mine!");
       	return 1;
	}
	new Float:X, Float:Y, Float:Z;
	GetPlayerPos(playerid, X, Y, Z);
	mine[playerid][0] = CreateObject(19290, X, Y, Z-0.85, 0.0, 0.0, 0.0);//2992
	mine[playerid][1] = CreateObject(19602, X, Y, Z-0.85, 0.0, 0.0, 0.0);//19290
	status[playerid] = 1;
	minecount[playerid] = 11;
	dtext(playerid);
  	return 1;

}

CMD:ahide(playerid,params[])
{
	if(PlayerInfo[playerid][pAdmin] > 0)
	{
	    aHide[playerid] = true;
		return 1;
	}
	else
	{
		SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]:You need to be admin to use this command!");
	}
	return 1;
}

CMD:ashow(playerid,params[])
{
	if(PlayerInfo[playerid][pAdmin] > 0)
	{
	    aHide[playerid] = false;
		return 1;
	}
	else
	{
		SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]:You need to be admin to use this command!");
	}
	return 1;
}

CMD:admins(playerid,params[])
{
    new string[128];
    new sendername[MAX_PLAYER_NAME];
    new count=0;
	if(IsPlayerConnected(playerid))
	{
		for (new i = 0; i < MAX_PLAYERS; i++)
		{
			if(IsPlayerConnected(i))
			{
				if(PlayerInfo[i][pAdmin] > 0 && aHide[i] == false)
				{
					GetPlayerName(i , sendername, sizeof(sendername));
					format(string, sizeof(string),"%sLevel %d: %s\n", string, PlayerInfo[i][pAdmin], sendername);
					count++;
				}
			}
		}
		if (count==0)
		{
		    format(string,sizeof(string),"No admins online at the moment");
		}
		ShowPlayerDialog(playerid,DIALOG_ADMINS,DIALOG_STYLE_MSGBOX,"Admins Online",string,"Ok","Back");
	}
	return 1;
}
//============================================================================//
//==================================CMDS======================================//
CMD:fps(playerid, params[])
{
    //SendClientMessageEx(playerid, COLOR_LIME, "If you think you cannot use /fps , Please /resetfps to fix it up. Then use /fps again."); //Depend on you that you will use it or not.
    if(FPS[playerid] == false) // You can use FPS[playerid] == 0) 0=False.
    {
        FPS[playerid] = true;//Same as before, You can use 1 tho.
        StartFPS(playerid);//Called that StartFPS stock to use FPS.
    }
    else if(FPS[playerid] == true)// Same as before, you can use 1 here.
    {
        FPS[playerid] = false;// You can use 0 if you don't want to false.
        StopFPS(playerid);//Called Stock StopFPS
    }
    return 1;
}

CMD:resetfps(playerid, params[])
{
    Reset(playerid);
    return 1;
}

CMD:help(playerid, params[])
{
	ShowPlayerDialog(playerid,23,DIALOG_STYLE_MSGBOX ,"Help","{4DC1E8}Welcome to our server!\n\n{FFFFFF}Main point is to get more kills then opponent team\nAfter 5 minutes round is over and map is changing\nTry to get more kills in a row in order to get bonus score\nFor more help see {4DC1E8}/cmds /rules {FFFFFF}or ask admins online!","Nice!","");
	return 1;
}

CMD:ahelp(playerid,params[])
{
	if(PlayerInfo[playerid][pAdmin] == 1)
	{
	    ShowPlayerDialog(playerid,10,DIALOG_STYLE_MSGBOX,"Admin Level 1 Commands","/ban\n/kick\n/mute\n/ann\n/mute\n/unmute\n/ip/n/cc\n/asay\n/achat","Nice","");
	}
	if(PlayerInfo[playerid][pAdmin] == 2)
	{
	    ShowPlayerDialog(playerid,11,DIALOG_STYLE_MSGBOX,"Admin Level 2 Commands","/ban\n/kick\n/mute\n/ann\n/mute\n/unmute\n/ip/n/cc\n/asay\n/achat\n/slap\n/explode\n/akill","Nice","");
	}
	if(PlayerInfo[playerid][pAdmin] == 3)
	{
	    ShowPlayerDialog(playerid,12,DIALOG_STYLE_MSGBOX,"Admin Level 3 Commands","/ban\n/kick\n/mute\n/ann\n/mute\n/unmute\n/ip/n/cc\n/asay\n/achat\n/slap\n/explode\n/akill\n/goto\n/get","Nice","");
	}
	if(PlayerInfo[playerid][pAdmin] == 4)
	{
	    ShowPlayerDialog(playerid,13,DIALOG_STYLE_MSGBOX,"Admin Level 4 Commands","/ban\n/kick\n/mute\n/ann\n/mute\n/unmute\n/ip/n/cc\n/asay\n/achat\n/slap\n/explode\n/akill\n/goto\n/get\n/freeze\n/unfreeze","Nice","");
	}
	if(PlayerInfo[playerid][pAdmin] == 5)
	{
	    ShowPlayerDialog(playerid,14,DIALOG_STYLE_MSGBOX,"Admin Level 5 Commands","{4DC1E8}/ban\n/{FFFFFF}kick\n/{4DC1E8}mute\n/{FFFFFF}ann\n/{4DC1E8}mute\n{FFFFFF}/unmute\n{4DC1E8}/ip\n/{FFFFFF}cc\n/{4DC1E8}asay\n/{FFFFFF}achat\n{4DC1E8}/slap\n/{FFFFFF}explode\n{4DC1E8}/akill\n/{FFFFFF}goto\n/{4DC1E8}get\n/{FFFFFF}freeze\n/{4DC1E8}unfreeze\n/{FFFFFF}jetpack\n/{4DC1E8}restart\n/{FFFFFF}changemap","Nice","");
	}
	if(PlayerInfo[playerid][pAdmin] == 0)
	{
	    SendClientMessage(playerid,COLOR_RED,"[{F7B62A}ERROR{FFFFFF}]:You need to be admin to use this command!");
	}
	return 1;
}

CMD:team(playerid,params[])
{
  	ForceClassSelection(playerid);
  	SetPlayerHealth(playerid,0.0);
	return 1;
}

CMD:pm(playerid, params[])
{
	new str[256], str2[256], id, Name1[MAX_PLAYER_NAME], Name2[MAX_PLAYER_NAME];
	if(sscanf(params, "us", id, str2))
	{
	    SendClientMessage(playerid,COLOR_WHITE, "[{F7B62A}USAGE{FFFFFF}]: {F7B62A}/pm {FFFFFF}<{F7B62A}id{FFFFFF}> <{F7B62A}message{FFFFFF}>");
	    return 1;
	}
	if(!IsPlayerConnected(id)) return SendClientMessage(playerid, COLOR_WHITE, "[{F7B62A}ERROR{FFFFFF}]: Player not connected");
	if(playerid == id) return SendClientMessage(playerid, COLOR_WHITE, "[{F7B62A}ERROR{FFFFFF}]: You cannot pm yourself!");
	{
		GetPlayerName(playerid, Name1, sizeof(Name1));
		GetPlayerName(id, Name2, sizeof(Name2));
		format(str, sizeof(str), "{FFFFFF}[{F7B62A}PM{FFFFFF} To {F7B62A}%s{FFFFFF}({F7B62A}ID %d{FFFFFF}): %s", Name2, id, str2);
		SendClientMessage(playerid, 0xFF0000FF, str);
		format(str, sizeof(str), "[{F7B62A}PM]{FFFFFF} From {F7B62A}%s {FFFFFF}(ID {F7B62A}%d{FFFFFF}): %s", Name1, playerid, str2);
		SendClientMessage(id, COLOR_WHITE, str);
		ReplyId[id] = playerid;
	}
	return 1;
}

CMD:r(playerid,params[])
{
	new targetid = ReplyId[playerid];
	ReplyId[targetid] = playerid;
	return 1;
}

CMD:t(playerid,params[])
{
	new text[128],pName[MAX_PLAYER_NAME],formatted[156];
	if(sscanf(params,"s[128]",text)) return SendClientMessage(playerid,-1,"Usage: /t [text]");
	GetPlayerName(playerid,pName,sizeof(pName));
	format(formatted,sizeof(formatted),"[{F7B62A}Team{FFFFFF}]: %s(%d): %s",pName,playerid,text);
	TeamChat(playerid,formatted);
	return 1;
}

CMD:class(playerid,params[])
{
	TextDrawShowForPlayer(playerid,Textdraw0);
	TextDrawShowForPlayer(playerid,Textdraw1);
	TextDrawShowForPlayer(playerid,Textdraw2);
	TextDrawShowForPlayer(playerid,Textdraw3);
	TextDrawShowForPlayer(playerid,Textdraw4);
	TextDrawShowForPlayer(playerid,Textdraw5);
	TextDrawShowForPlayer(playerid,Textdraw6);
	TextDrawShowForPlayer(playerid,Textdraw7);
	TextDrawShowForPlayer(playerid,Textdraw8);
	TextDrawShowForPlayer(playerid,Textdraw9);
	SelectTextDraw(playerid, 0xA3B4C5FF);
	return 1;
}
