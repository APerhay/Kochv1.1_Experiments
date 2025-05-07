# Kochv1.1_Experiments

This project is orieneted towards experimeting with the Koch v1.1 robot and Lerobot using example 7 from the lerobot github:
  https://github.com/huggingface/lerobot/blob/main/examples/7_get_started_with_real_robot.md

I am using a windows system, using AnacondaPrompt

I downloaded miniconda to run a python 3.10 vitrual envoirnment as shown on the lerobot github.
installing miniconda: https://www.anaconda.com/docs/getting-started/miniconda/install#windows-power-shell

in order to use the conda command I had to use Anaconda Prompt as my CLI

I had to install git using;
winget install Microsoft.Git

close and open the terminal and then clone lerobot using
git clone https://github.com/huggingface/lerobot.git

i followed the tutorial to install lerobot with no issues, then moved onto example 7 for using real robots.
ran pip to install dynamixel
I have had to use a notepad to turn the copied code into one line for one like this:
python lerobot/scripts/control_robot.py \
  --robot.type=koch \
  --control.type=teleoperate
into this:
python lerobot/scripts/control_robot.py --robot.type=koch --control.type=teleoperate

after identifying ports i went into my file system to 
lerobot/lerobot/common/robot_devices/robots/configs.py
and modified it in visual studio, went to the 
KochRobotConfig class
and replaced the default ports with my collected ports, COM3 for leader, COM4 for follower. and saved the file

for the interactive python session i entered "Python" into the line, following by the copied code after i modified it in notepad
I recieved the arms with motors already indexed properly so i had no configuration for the indexing

after that following the tutorial i was able to teleoperate with no problems.

at this point i realized i needed to create a huggingface dataset, and get a token for this script to write to the data set.
after making the dataset and a token for read/write access into it i made sure i had the proper library installed by running
  pip install -U "huggingface_hub[cli]"
after that i ran
  huggingface-cli login --token ${HUGGINGFACE_TOKEN} --add-to-git-credential
replacing the "${HUGGINGFACE_TOKEN}" with my generated token from hugging face

i moved onto configuring the  OpenCVCamera  so that I could use my phone as a virtual camera

I downloaded droid cam on my phone and obs studio on my laptop
obs studio:
  https://obsproject.com/download
i then installed the droidcam polugin for obs:
  https://github.com/dev47apps/droidcam-obs-plugin/releases
afterwards i followed this tutorial
  https://droidcam.app/obs/usage/

after i got droidcam working and the source setup appropriately i started the virtual camera and ran
  --images-dir outputs/images_from_opencv_cameras
luckily both my webcam and the virtual camera were detected with no issues.

I began setting up my area prepping for testing using my iphone and laptop for the videos, and my dataset on hugging face to store the data, the tutorial introduced the line
  python lerobot/scripts/control_robot.py --robot.type=koch --control.type=teleoperate
which began teleoperation continuously from the CLI i have been using ctrl+c to interrupt it and stop the teleoperation

at this point I have been able to record and replay episodes with no issue, the next step will be to record enough episode(starting at around 50) to train a policy on
after all of the configuration I have collected these useful lines of code


teleoperation until interrupted
  python lerobot/scripts/control_robot.py --robot.type=koch --control.type=teleoperate

record episodes
  python lerobot/scripts/control_robot.py --robot.type=koch --control.type=record --control.single_task="Grasp a lego block and put it in the bin." --control.fps=30 --control.repo_id=Aperhay/kochv1.1_lerobot_test1 --control.warmup_time_s=5 --control.episode_time_s=30 --control.reset_time_s=15 --control.num_episodes=2 --control.push_to_hub=true

visualize episodes
  python lerobot/scripts/visualize_dataset_html.py --repo-id Aperhay/kochv1.1_lerobot_test1

replay episode
  python lerobot/scripts/control_robot.py --robot.type=koch --control.type=replay --control.fps=30 --control.repo_id=Aperhay/kochv1.1_lerobot_test1 --control.episode=0

