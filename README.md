# ReCDroid

[![MIT License](https://img.shields.io/github/license/xiaocong/uiautomator.svg)](http://opensource.org/licenses/MIT)


ReCDRoid is software tool to automatically reproduce crashes from Android bug reports. 

## A Qick YouTube Demo of RecDroid

Given a bug report description: https://github.com/milesmcc/LibreNews-Android/issues/22: 
- `Generate a reproducing script (an event sequence) leading to the crash` https://youtu.be/i0fQ58aUPa8 
- `Replay the reproducing sequence` https://youtu.be/WVtp-Ld4WF0 
           
## Artifact Description

This is the artifact is for the paper:  ReCDroid: Automatically Reproducing Android
Application Crashes from Bug Reports, ICSE 2019. 

The artifcat shows:

1) ReCDroid, a functioning tool to automatically reproduce crashes from Android bug reports (Table II of Section V). 
2) Experiment results generated by ReCDroid presented in the paper. 
3) Programs (i.e., APKs) and bug reports used in the paper. 
4) Documentation showing how ReCDroid can be used. 

## Run RecDroid Using a VirtualBox Image

### Pre-requirements

- Prepare a desktop computer with the following recommended configuration: 32GB memory, 8 kernels cpu, more than 50 GB free disk.
- Download [24 GB VirtualBox image](https://drive.google.com/file/d/1kKdHsa9TJEyU4pYyOQPyMzZqagWBcVSX/view?usp=sharing). The root password of this image is `rec_xxx`.
- Use VirtualBox(we use 5.1.38) to import the VirtualBox image.

### Run RecDroid

1. Open the virtualbox image.
2. Start an android emulator. Wait (for minutes) until it is totally launched.
```sh
   emulator -avd avd -wipe-data
```
3. Enter the Artifact folder, in which we have 25 bug report folders with App name on it.
```sh
   cd ~/Artifact-Evaluation/1.worked-in-this-VM/Android-4.4.2
   ls
```
   results:
```sh
10.olam1_'_s            17.transistor_s     24.obdreader_s  6.acv_s
11.olam_space_s         18.zom              26.odk          7.anymemo_deafult_s
12.fastadaper_s         19.Pix-Art-share_s  27.k-9_s        8.anymemo_menu_s
13.librenews_refresh_s  1.newsblur_s        2.markor_s      9.nodepad_s
14.librenews_back_s     20.Pix-Art_s        3.birthdroid_s
15.librenews_s          22.ventriloid_s     4.carreport_s
16.smssync_s            23.news_s           5.opensudoku_s
```
4. Go to a bug report folder, e.g., 1.newsblur_s.
```sh
   cd 1.newsblur_s
```

5. Run the bug reproducing process, which is named with "percerun_`AppName`.sh", to generate a reproducing script. 
```sh
   ./percerun_newsblur.sh
```
   In this step and on the emulator, ReCDRoid will explore the app and try to reproduce crashes based on the description of bug report. The display on the emulator should be similar but may not exactly the same as the [`Reproducing process video`](https://youtu.be/i0fQ58aUPa8) (it is for another bug report). Please wait until it shows "matchcrash" and "execution time". It may run more than 1 hour for some difficult bugs.

6. Replay the reproducing script showing how to trigger the crash with "runCrash_`AppName`.sh ".
```sh
   ./runCrash_newsblur.sh
```
   The display on the emulator should be similar but not exactly the same as [`Reproducing result video`](https://youtu.be/WVtp-Ld4WF0) video (it is for another bug report). After you observe the event sequence on the emulator, you can manually repeat it on the app. This event sequence can trigger a crash as described in the bug report.
   
7. Close all of the terminals and emulator before you want to test another bug report.

### Troubleshooting

If you see a message "failure" as an intermediate output during the execution of ReCDroid, please just ignore them.

This VirtualBox image has a default setting: 15GB memory, 4 kernels cpu. If your desktop runs this VirtualBox image very slowly, you may consider modifing its default setting to use fewer kernels cpu or less memory.

ReCDroid may fail to reproduce crashes for some bug reports in VirtualBox on a system with low-performance hardware. This is because if the VituralBox image runs on poor hardware, the emulator may be very slow and the response time for a click event may take more than 3 seconds, which exceeds the maximum response time specified by ReCDroid. In this case, ReCDroid will termintate the reproducing process. Users are suggested to either configure ReCDroid on the physical machine or move the VirtualBox image to a faster system. 

## Run ReCDroid on Physical Machine

It is worth noting that running Android emulator in a VirtualBox ubuntu system is not ideal due to these [reasons](https://stackoverflow.com/questions/14971621/android-emulator-not-starting-in-a-virtualbox-ubuntu-instance). The emulator in  VirtualBox is much slower than a physical machine. In addition, some emulators with resource consuming android SDK versions may generate launch errors, such as Android 7.0.0, or can not be launched at all, such as Android 5.0.1 and 5.1.1. In our case, 25 out of 33 reproduced bug reports (presented in the paper) can be successfully run in VirtualBox. If you want to use ReCDroid to reproduce all 33 bug reports, you should prepare a Ubuntu 16.04 physical machine and build the execution environment following  this [instruction](https://drive.google.com/file/d/1cCYkYyc8XQlI81RCSMe3fx-E8AMFypWD/view?usp=sharing). 


##  Description of Result Files Generated By ReCDroid:

We use one bug report folder `1.newsblur_s/` as an example.

### Input file

- Bug report: `nlpBugReport/percentbugreport`.

- Input APK: `newsblur-v6.10_debug.apk`.

- Robotium file: `BFS_androidtester.apk`. The APK's package name and MainActivityName should be clarified in it before it is complied

- Shell file: `percerun_newsblur.sh`. The AAPKpk's package name and MainActivityName should be clarified in it.

### Output file

- NLP result: `middleResults/nlp.xml`.

- Event flow graph after exploration: `middleResults/record.xml`.

- Reproducing script to trigger the crashes, which has an event sequence: `middleResults/run.xml`.

## Example:

A bug report in https://github.com/milesmcc/LibreNews-Android/issues/22, whose description is as follows:

- Install v1.4 from FDroid.
- Launch app.
- Disable automatically refresh
- Change server address to an invalid one, e.g., xxyyzz.
- Click refresh.

### Videos:

`Exploration`: example1-exploration.mkv

Youtube link: https://youtu.be/i0fQ58aUPa8

`Replay`: example2-runTestCase.mkv

Youtube link: https://youtu.be/WVtp-Ld4WF0


###  Output Test Cases:

Output steps in run.xml (i.e., reproducing script): 

```
<step id="1"><currentClass>1</currentClass><subId>1</subId>...</step>
 
<step id="2"><currentClass>2</currentClass><subId>5</subId>...</step>

<step id="3"><currentClass>3</currentClass><subId>4</subId>...</step>

<step id="4"><currentClass>4</currentClass><subID>1</subID>...</step>

<step id="5"><currentClass>4</currentClass><subId>3</subId>...</step>

<step id="6"><currentClass>3</currentClass><subId>1</subId>...</step>
```

In the above example, there are 6 steps for ReCDroid to trigger a crash. The `currentClass` is a page in the Android app. The detailed information of a page can be found in the record.xml file with (CurrentClass in run.xml)=(ID in record.xml). I apologize for the naming confusions. It will be updated in the next version. Every page has a list of UI components, so the subID is the component ID in this page. The detailed information of UI components can also be found in record.xml with (subID in run.xml)=(runableID in record.xml). Followed by subID, there are a few other parameters to help developers understand how to active this step.

## Other Content Included in This Repository

- Evaluation Results: We provide more detailed evaluation results besides those presented in the paper. We also provide the set of APKs used in the experiment. 
- Bug Report Analysis: We crawled over 10000 Android bug reports and summarize 22 NLP grammar rules.
- NLP Patterns: We use the summarized rules to build NLP patterns,  which can analyzing the bug reports.
- Source Code: All source files of ReCDroid. In this folder, Robotium source code can support Android SDK lower than Android 6.0. The UIAutomator source code can support Android SDK higher than or equal to Android 6.0.


