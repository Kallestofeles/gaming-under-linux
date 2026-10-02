# Ignition (1997)
PCGW: https://www.pcgamingwiki.com/wiki/Ignition  
Source platform: GOG  
Launcher: Heroic  

## Pre-requisites
- ffmpeg

## Running 3dfx version on Proton
- Install the Windows version via Heroic launcher (it gets the DOS version, but that is NOT the same as Steam's version)  
- Download the 3dfx patch for Ignition:  
[web_archive_link](https://web.archive.org/web/20000903035503/http://www.uds.se/ignition/ign_3dfx3.zip)
  - Extract the `Ign_3dfx.exe` from the archive into the root directory of the game
  - Extract `general` and `levels` folders into a clean new directory - we will be modifying those files next before injecting them into the installation dir
    - Run the following script within **BOTH extracted** `general` and `levels` directories (it will simply turn the filenames all UPPERCASE - this is needed because unlike Windows, Linux IS case-sensitive)  
    ```find . -depth -execdir bash -c 'for f; do mv -- "$f" "${f^^}"; done' bash {} + ```  
    (*yes, this is AI-slop command, but it works for this objective*)
    - Now that you have everything UPPERCASED, copy over the contents of the two directories into the respective folders in the game's root directory
- Go to `[IGNITION_ROOT_DIR]/BALTAZAR/DATA/` and make copies of the following files with the following names:
  - `DEFAULT2.PSQ` > copy to > `DEFAULT.PSQ`
  - `TEST2.PFM` > copy to > `TEST.PFM`  
  This is needed as the Windows version has different startup video names which otherwise cause the game to crash during startup.
- Download nGlide (render 3dfx graphics on modern hardware):  
https://www.zeus-software.com/downloads/nglide
  - Use Heroic's "RUN EXE ON PREFIX" to install nGlide
  - Use Heroic's "RUN EXE ON PREFIX" to launch nGlide setup which is found here:  
  `[WINEPREFIX]/drive_c/windows/syswow64/nglide_config.exe`
- Launch the game via Heroic and enjoy

## Getting the music to play
I spent way too much time trying to get the game's music to play, but in the end, this is how I got it working.
- Download [cdaudio-winmm](https://github.com/dippy-dipper/cdaudio-winmm/releases)
  - Extract `winmm.dll` file and `mcicda` directory into the game's root installation directory
  - Run the following ffmpeg conversion script in the `[IGNITION_ROOT_DIR]`:
    ```bash
    for f in Music/*.ogg; do n=$(basename "$f" .ogg); ffmpeg   -i "$f" -c:a pcm_s16le "mcicda/music/${n}.wav"; done
    ```  
    It takes the Track0*.ogg files, converts them with ffmpeg to something that `cdaudio-winmm` can handle and then outputs them into `mcicda/music/` directory.
- Set the following environment variable in Heroic:

  |KEY/NAME|VALUE|
  |---|---|
  |WINEDLLOVERRIDES|winmm=n,b|
