# Folder Structure and Python File Content for: `/home/matteo/src/Pepper`

* **modim/**
* **pepper_tools/**
  * **.git/**
    * **branches/**
    * **hooks/**
    * **info/**
    * **logs/**
      * **refs/**
        * **heads/**
        * **remotes/**
          * **origin/**
    * **objects/**
      * **info/**
      * **pack/**
    * **refs/**
      * **heads/**
      * **remotes/**
        * **origin/**
      * **tags/**
  * **animation/**
    * `animation.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      import qi
      import argparse
      import sys
      import time
      import os
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["Memory Read", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          animation_player_service = session.service("ALAnimationPlayer")
          animation_player_service.run("animations/Stand/Gestures/Hey_1")
      
      if __name__ == "__main__":
          main()
      ```
      </details>

  * **asr/**
    * `asr.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/audio/alspeechrecognition-api.html
      import qi
      import argparse
      import sys
      import os
      
      def onWordRecognized(value):
          print "value=",value
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["ReactToTouch", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          asr_service = session.service("ALSpeechRecognition")
          asr_service.setLanguage("English")
      
          memory_service  = session.service("ALMemory")
      
          #establishing test vocabulary
          vocabulary = ["yes", "no", "please", "hello", "goodbye", "hi, there", "go to the kitchen"]
          asr_service.setVocabulary(vocabulary, False)
      
          # Start the speech recognition engine with user Test_ASR
          asr_service.subscribe("Test_ASR")
          print 'Speech recognition engine started'
      
          #subscribe to event WordRecognized
          subWordRecognized = memory_service.subscriber("WordRecognized")
          idSubWordRecognized = subWordRecognized.signal.connect(onWordRecognized)
      
          #let it run
          app.run()
      
          #Disconnecting callbacks and subscribers
          asr_service.unsubscribe("Test_ASR")
          subWordRecognized.signal.disconnect(idSubWordRecognized)
      
      if __name__ == "__main__":
          main()
      ```
      </details>

    * `human_say.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      import qi
      import argparse
      import sys
      import time
      import os
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--sentence", type=str, default="hello",
                              help="Sentence said by human")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["HumanSay", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          memory_service  = session.service("ALMemory")
      
          fakeASRkey = 'FakeRobot/ASR'
          fakeASRevent = 'FakeRobot/ASRevent'
          fakeASRtimekey = 'FakeRobot/ASRtime'
      
          tm = int(time.time())  # does not work with float!!!
          memory_service.raiseEvent(fakeASRevent, args.sentence)
          memory_service.insertData(fakeASRkey, args.sentence)
          memory_service.insertData(fakeASRtimekey, tm)
      
          print("Human Say: '%s' at time %d" %(args.sentence,tm))
      
      if __name__ == "__main__":
          main()
      ```
      </details>

  * **audio/**
    * `audio_player.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      import qi
      import argparse
      import sys
      import os
      import time
      
      def main():
      
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--afile", type=str, help="Audio file to play")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          afile = args.afile
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              print "Connecting to ",	connection_url
              app = qi.Application(["Memory Write", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          ap_service = session.service("ALAudioPlayer")
      
          try:
              #Loads a file and launchs the playing 5 seconds later
              fileId = ap_service.loadFile(os.path.abspath(afile))
              fileLength =ap_service.getFileLength(fileId)
              print 'Playing '+afile+'. Duration: '+ str(fileLength) +' secs. Press Ctrl+C to stop'
              ap_service.play(fileId, _async = True)
              time.sleep(fileLength)
          except KeyboardInterrupt:
              ap_service.stopAll()
              print('Quitting')
              sys.exit(0)
      
      if __name__ == "__main__":
          main()
      ```
      </details>

  * **bags/**
  * **behaviors/**
    * `behavior.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      # -*- encoding: UTF-8 -*-
      
      import sys
      import time
      import argparse
      import os
      import qi
      
      from naoqi import ALProxy
      
      def executeBehavior(beh_service, behaviorName):
        if (behaviorName is None):
          behaviorName = ".lastUploadedChoregrapheBehavior/behavior_1"
      
        # run the behaviot
        launchAndStopBehavior(beh_service, behaviorName)
      
        #add as a default behavior
        #defaultBehaviors(beh_service, behaviorName)
      
      def stopBehavior(beh_service, behaviorName):
        # Stop the behavior.
        if (beh_service.isBehaviorRunning(behaviorName)):
          beh_service.stopBehavior(behaviorName)
          time.sleep(1.0)
        else:
          print "Behavior is already stopped."
      
      def getBehaviors(beh_service):
        ''' Know which behaviors are on the robot '''
      
        names = beh_service.getInstalledBehaviors()
        print "Behaviors on the robot:"
        print names
      
        names = beh_service.getDefaultBehaviors()
        print "Default behaviors:"
        print names
      
        names = beh_service.getRunningBehaviors()
        print "Running behaviors:"
        print names
      
      def launchAndStopBehavior(beh_service, behaviorName):
        ''' Launch and stop a behavior, if possible. '''
      
        # Check that the behavior exists.
        if (beh_service.isBehaviorInstalled(behaviorName)):
      
          # Check that it is not already running.
          if (not beh_service.isBehaviorRunning(behaviorName)):
            # Launch behavior. This is a blocking call, use post if you do not
            # want to wait for the behavior to finish.
            beh_service.startBehavior(behaviorName)
            #time.sleep(10)
          else:
            print "Behavior is already running."
      
        else:
          print "Behavior not found."
          return
      
        #names = beh_service.getRunningBehaviors()
        #print "Running behaviors:"
        #print names
      
        #names = beh_service.getRunningBehaviors()
        #print "Running behaviors:"
        #print names
      
      def defaultBehaviors(beh_service, behaviorName):
        ''' Set a behavior as default and remove it from default behavior. '''
      
        # Get default behaviors.
        names = beh_service.getDefaultBehaviors()
        print "Default behaviors:"
        print names
      
        # Add behavior to default.
        beh_service.addDefaultBehavior(behaviorName)
      
        names = beh_service.getDefaultBehaviors()
        print "Default behaviors:"
        print names
      
        # Remove behavior from default.
        beh_service.removeDefaultBehavior(behaviorName)
      
        names = beh_service.getDefaultBehaviors()
        print "Default behaviors:"
        print names
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--start", type=str, default=None, help="start behavior")
          parser.add_argument("--stop", type=str, default=None, help="stop behavior")
          parser.add_argument('--list', help='List available behaviors', action='store_true')
      
          args = parser.parse_args()
      
          #Starting application
          try:
              connection_url = "tcp://" + args.pip + ":" + str(args.pport)
              app = qi.Application(["Behavior ", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + args.pip + "\" on port " + str(args.pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
      
          beh_service = app.session.service("ALBehaviorManager")
      
          if (args.list):
              getBehaviors(beh_service)
          elif (args.start is not None):
              executeBehavior(beh_service,args.start)
          elif (args.stop is not None):
              stopBehavior(beh_service,args.stop)
      
      if __name__ == "__main__":
          main()
      
      ```
      </details>

    * `behavior_background.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      import sys
      import time
      import argparse
      import os
      import qi
      
      from naoqi import ALProxy
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--enable", type=int, default=1,
                              help="(0=deactivate, 1=activate)")
      
          args = parser.parse_args()
      
          #Starting application
          try:
              connection_url = "tcp://" + args.pip + ":" + str(args.pport)
              app = qi.Application(["Behavior ", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + args.pip + "\" on port " + str(args.pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
      
          enable = args.enable
      
          bm_service = app.session.service("ALBackgroundMovement")
          bm_service.setEnabled(enable)
      
          ba_service = app.session.service("ALBasicAwareness")
          ba_service.setEnabled(enable)
      
          sm_service = app.session.service("ALSpeakingMovement")
          sm_service.setEnabled(enable)
      
      if __name__ == "__main__":
          main()
      
      ```
      </details>

  * **cmd_server/**
    * `pepper_cmd.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #!/usr/bin/env python
      
      # animations
      # http://doc.aldebaran.com/2-5/naoqi/motion/alanimationplayer-advanced.html#animationplayer-list-behaviors-pepper
      
      # How to use
      
      # export PEPPER_IP=<...>
      # python
      # >>> import pepper_cmd
      # >>> from pepper_cmd import *
      # >>> begin()
      # >>> pepper_cmd.robot.<fn>()
      # >>> end()
      
      import time
      import os
      import socket
      import threading
      import math
      import random
      import datetime
      from datetime import datetime
      
      import qi
      from naoqi import ALProxy
      
      # Python Image Library
      from PIL import Image
      
      laserValueList = [
        # RIGHT LASER
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg01/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg01/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg02/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg02/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg03/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg03/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg04/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg04/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg05/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg05/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg06/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg06/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg07/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg07/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg08/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg08/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg09/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg09/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg10/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg10/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg11/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg11/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg12/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg12/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg13/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg13/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg14/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg14/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg15/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg15/Y/Sensor/Value",
        # FRONT LASER
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg01/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg01/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg02/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg02/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg03/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg03/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg04/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg04/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg05/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg05/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg06/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg06/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg07/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg07/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg08/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg08/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg09/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg09/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg10/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg10/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg11/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg11/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg12/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg12/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg13/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg13/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg14/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg14/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg15/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg15/Y/Sensor/Value",
        # LEFT LASER
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg01/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg01/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg02/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg02/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg03/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg03/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg04/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg04/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg05/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg05/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg06/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg06/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg07/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg07/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg08/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg08/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg09/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg09/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg10/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg10/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg11/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg11/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg12/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg12/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg13/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg13/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg14/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg14/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg15/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg15/Y/Sensor/Value"
      ]
      
      app = None
      session = None
      tts_service = None
      memory_service = None
      motion_service = None
      anspeech_service = None
      tablet_service = None
      
      robot = None        # PepperRobot object
      
      RED   = "\033[1;31m"
      BLUE  = "\033[1;34m"
      CYAN  = "\033[1;36m"
      GREEN = "\033[0;32m"
      RESET = "\033[0;0m"
      BOLD    = "\033[;1m"
      REVERSE = "\033[;7m"
      
      # Sensors
      headTouch = 0.0
      handTouch = [0.0, 0.0] # left, right
      sonar = [0.0, 0.0] # front, back
      
      # Sensors
      
      def sensorThread(robot):
          sonarValues = ["Device/SubDeviceList/Platform/Front/Sonar/Sensor/Value",
                        "Device/SubDeviceList/Platform/Back/Sonar/Sensor/Value"]
          headTouchValue = "Device/SubDeviceList/Head/Touch/Middle/Sensor/Value"
          handTouchValues = [ "Device/SubDeviceList/LHand/Touch/Back/Sensor/Value",
                         "Device/SubDeviceList/RHand/Touch/Back/Sensor/Value" ]
          frontLaserValues = [
            "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg07/X/Sensor/Value",
            "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg07/Y/Sensor/Value",
            "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg08/X/Sensor/Value",
            "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg08/Y/Sensor/Value",
            "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg09/X/Sensor/Value",
            "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg09/Y/Sensor/Value" ]
      
          t = threading.currentThread()
          while getattr(t, "do_run", True):
              robot.headTouch = robot.memory_service.getData(headTouchValue)
              robot.handTouch = robot.memory_service.getListData(handTouchValues)
              robot.sonar = robot.memory_service.getListData(sonarValues)
              laserValues = robot.memory_service.getListData(frontLaserValues)
              dd = 0 # average distance
              c = 0
              for i in range(0,len(laserValues),2):
                  px = laserValues[i] if laserValues[i] is not None else 10
                  py = laserValues[i+1] if laserValues[i+1] is not None else 0
                  d = math.sqrt(px*px+py*py)
                  if d<10:
                      dd = dd + d
                      c = c+1
      
              if (c>0):
                  robot.frontlaser = dd / c
              else:
                  robot.frontlaser = 10.0
      
              #print "Head touch middle value=", robot.headTouch
              #print "Hand touch middle value=", robot.handTouch
              #print "Sonar [Front, Back]", robot.sonar
              time.sleep(0.2)
          #print "Exiting Thread"
      
      def touchcb(value):
          print "value=",value
      
          touched_bodies = []
          for p in value:
              if p[1]:
                  touched_bodies.append(p[0])
      
          print touched_bodies
      
      asr_word = ''
      asr_confidence = 0
      asr_timestamp = 0
      
      def onWordRecognized(value):
          global asr_word, asr_confidence, asr_timestamp
          print "ASR value = ",value,time.time()
          if (value[1]>0 and value[0]!=''):
          #if (value[1]>0 and (value[0]!='' or time.time()-asr_timestamp>1.0)):
          #if (value[1]>0):
              asr_word = value[0]
              asr_confidence = value[1]
              asr_timestamp = time.time()
      
      def sensorvalue(sensorname):
          global robot
          if (robot!=None):
              return robot.sensorvalue(sensorname)
      
      touchcnt = 0
      
      # function called when the signal onTouchDown is triggered
      def touch_cb(x, y):
          global robot, touchcnt
          print "Touch coordinates are x: ", x, " y: ", y
          robot.screenTouch = (x,y)
          touchcnt = touchcnt + 1
          time.sleep(1)
          touchcnt = touchcnt - 1
          if touchcnt == 0:
              robot.screenTouch = (0.0,0.0)
      
      def laserMonitorThread (memory_service):
          t = threading.currentThread()
          while getattr(t, "do_run", True):
              laserValues =  memory_service.getListData(laserValueList)
              #print laserValues[44],laserValues[45] #X,Y values of central point
              time.sleep(0.2)
          print "Exiting Thread"
      
      # Begin/end
      
      def begin():
          global robot
          print 'begin'
          if (robot==None):
              robot=PepperRobot()
              robot.connect()
          robot.begin()
      
      def end():
          global robot
          print 'end'
          time.sleep(0.5) # make sure stuff ends
          if (robot!=None):
              robot.quit()
      
      # Robot motion
      
      def stop():
          global robot
          if (robot==None):
              begin()
          robot.stop()
      
      def forward(r=1):
          global robot
          if (robot==None):
              begin()
          robot.forward(r)
      
      def backward(r=1):
          global robot
          if (robot==None):
              begin()
          robot.backward(r)
      
      def left(r=1):
          global robot
          if (robot==None):
              begin()
          robot.left(r)
      
      def right(r=1):
          global robot
          if (robot==None):
              begin()
          robot.right(r)
      
      def robot_stop_request(): # stop until next begin()
          if (robot!=None):
              robot.stop_request = True
              robot.stop()
              print("stop request")
      
      # Wait
      
      def wait(r=1):
          print 'wait',r
          for i in range(0,r):
              time.sleep(3)
      
      # Sounds
      
      def bip(r=1):
          print 'bip'
      
      def bop(r=1):
          print 'bop'
      
      # Speech
      
      def say(strsay):
          global robot
          print 'Say ',strsay
          if (robot==None):
              begin()
          robot.say(strsay)
      
      def asay(strsay):
          global robot
          print 'Animated Say ',strsay
          if (robot==None):
              begin()
          robot.asay(strsay)
      
      # Other
      
      # Alive behaviors
      def setAlive(alive):
          global robot
          robot.setAlive(alive)
      
      def stand():
          global robot
          robot.stand()
      
      def disabled():
          global robot
          robot.disabled()
      
      def interact():
          global robot
          robot.interactive()
      
      def showurl(url):
          global robot
          if (robot!=None):
              return robot.showurl(url)
      
      def run_behavior(bname):
          global session
          beh_service = session.service("ALBehaviorManager")
          beh_service.startBehavior(bname)
          #time.sleep(10)
          #beh_service.stopBehavior(bname)
      
      def takephoto():
          global robot
          robot.takephoto()
      
      def opendiag():
          global robot
          robot.introduction()
      
      def sax():
          global robot
          robot.sax()
      
      class PepperRobot:
      
          def __init__(self):
              self.isConnected = False
              # Sensors
              self.headTouch = 0.0
              self.handTouch = [0.0, 0.0] # left, right
              self.sonar = [0.0, 0.0] # front, back
              self.frontlaser = 0.0
              self.screenTouch = (0,0)
              self.language = "English"
              self.stop_request = False
              self.frame_grabber = False
              self.face_detection = False
              self.got_face = False
      
              self.FER_server_IP = None
              self.FER_server_port = 5678
      
              self.logfile = None
      
              self.sensorThread = None
              self.laserThread = None
              self.lthr = None # log thread
      
              self.jointNames = ["HeadYaw", "HeadPitch",
                     "LShoulderPitch", "LShoulderRoll", "LElbowYaw", "LElbowRoll", "LWristYaw",
                     "RShoulderPitch", "RShoulderRoll", "RElbowYaw", "RElbowRoll", "RWristYaw",
                     "LHand", "RHand", "HipRoll", "HipPitch", "KneePitch"]
      
              self.fakeASRkey = 'FakeRobot/ASR'
              self.fakeASRtimekey = 'FakeRobot/ASRtime'
      
          def session_service(self,name):
              try:
                  return self.session.service(name)
              except:
                  print("Service %s not available." %(name))
                  return None
      
          # Connect to the robot
          def connect(self, pip=os.environ['PEPPER_IP'], pport=None, alive=False):
      
              self.ip = pip
              if pport is not None:
                  self.port = pport
              elif 'PEPPER_PORT' in os.environ:
                  self.port = int(os.environ['PEPPER_PORT'])
              else:
                  self.port = 9559
      
              if (self.isConnected):
                  print("Robot already connnected.")
                  return
      
              print("Connecting to robot %s:%d ..." %(self.ip,self.port))
              try:
                  connection_url = "tcp://" + self.ip + ":" + str(self.port)
                  self.app = qi.Application(["Pepper command", "--qi-url=" + connection_url ])
                  self.app.start()
              except RuntimeError:
                  print("%sCannot connect to Naoqi at %s:%d %s" %(RED,self.ip,self.port,RESET))
                  self.session = None
                  return
      
              print("%sConnected to robot %s:%d %s" %(GREEN,self.ip,self.port,RESET))
              self.session = self.app.session
      
              print("Starting services...")
      
              #Starting services
              self.memory_service  = self.session.service("ALMemory")
              self.motion_service  = self.session.service("ALMotion")
              self.tts_service = self.session.service("ALTextToSpeech")
              self.anspeech_service = self.session.service("ALAnimatedSpeech")
              self.leds_service = self.session.service("ALLeds")
              self.asr_service = None
              self.tablet_service = None
              self.bm_service = None
              try:
                  self.asr_service = self.session.service("ALSpeechRecognition")
                  self.tablet_service = self.session.service("ALTabletService")
                  self.touch_service = self.session.service("ALTouch")
                  self.animation_player_service = self.session.service("ALAnimationPlayer")
                  self.beh_service = self.session.service("ALBehaviorManager")
                  self.al_service = self.session.service("ALAutonomousLife")
                  self.rp_service = self.session.service("ALRobotPosture")
                  self.bm_service = self.session.service("ALBackgroundMovement")
                  self.ba_service = self.session.service("ALBasicAwareness")
                  self.sm_service = self.session.service("ALSpeakingMovement")
                  self.audiorec_service = self.session.service("ALAudioRecorder")
                  self.audio_service = self.session.service("ALAudioDevice")
                  self.battery_service = self.session.service("ALBattery")
                  self.people_service = self.session.service("ALPeoplePerception")
      
              except:
                  pass
      
              if self.bm_service!=None:
                  self.alive = alive
                  print('Alive behaviors: %r' %self.alive)
                  if self.bm_service!=None:
                      self.bm_service.setEnabled(self.alive)
                  if self.ba_service!=None:
                      self.ba_service.setEnabled(self.alive)
                  if self.sm_service!=None:
                      self.sm_service.setEnabled(self.alive)
      
              if self.tablet_service!=None:
                  webview = "http://198.18.0.1/apps/spqrel/index.html"
                  self.tablet_service.showWebview(webview)
                  self.touchsignalID = self.tablet_service.onTouchDown.connect(touch_cb)
                  self.touchstatus = self.touch_service.getStatus()
                  #print touchstatus
                  self.touchsensorlist = self.touch_service.getSensorList()
                  #print touchsensorlist
      
              self.isConnected = True
      
          def quit(self):
              print "Quit Pepper robot."
              self.sensorThread = None
              self.laserThread = None
              if self.sensorThread != None:
                  self.sensorThread.do_run = False
                  self.sensorThread = None
              if self.laserThread != None:
                  self.laserThread.do_run = False
                  self.laserThread = None
      
              if self.session!=None and self.tablet_service!=None:
                  self.tablet_service.onTouchDown.disconnect(self.touchsignalID)
              time.sleep(1)
              self.app.stop()
      
          # general commands
      
          def begin(self):
              self.stop_request = False
              self.ears_led(False)
              self.white_eyes()
      
          def exec_cmd(self, params):
              cmdstr = "self."+params
              print "Executing %s" %(cmdstr)
              eval(cmdstr)
      
          def tablet_home(self):
              webview = "http://198.18.0.1/apps/spqrel/index.html"
              self.tablet_service.showWebview(webview)
      
          # Network
      
          def networkstatus(self):
              # TODO
              #self.tablet_service.configureWifi(const std::string& security, const std::string& ssid, const std::string& key)
              #connectWifi(const std::string& ssid)
              return self.tablet_service.getWifiStatus()
      
          def robotIp(self):
              return self.tablet_service.robotIp() # just tablet IP ...
      
          # Leds
      
          def white_eyes(self):
              # white face leds
              self.leds_service.on('FaceLeds')
      
          def green_eyes(self):
              if self.leds_service!=None:
                  # green face leds
                  self.leds_service.on('LeftFaceLedsGreen')
                  self.leds_service.off('LeftFaceLedsRed')
                  self.leds_service.off('LeftFaceLedsBlue')
                  self.leds_service.on('RightFaceLedsGreen')
                  self.leds_service.off('RightFaceLedsRed')
                  self.leds_service.off('RightFaceLedsBlue')
      
          def red_eyes(self):
              if self.leds_service!=None:
                  # red face leds
                  self.leds_service.off('LeftFaceLedsGreen')
                  self.leds_service.on('LeftFaceLedsRed')
                  self.leds_service.off('LeftFaceLedsBlue')
                  self.leds_service.off('RightFaceLedsGreen')
                  self.leds_service.on('RightFaceLedsRed')
                  self.leds_service.off('RightFaceLedsBlue')
      
          def blue_eyes(self):
              if self.leds_service!=None:
                  # red face leds
                  self.leds_service.off('LeftFaceLedsGreen')
                  self.leds_service.off('LeftFaceLedsRed')
                  self.leds_service.on('LeftFaceLedsBlue')
                  self.leds_service.off('RightFaceLedsGreen')
                  self.leds_service.off('RightFaceLedsRed')
                  self.leds_service.on('RightFaceLedsBlue')
      
          def ears_led(self, enable):
              # Ears leds
              if enable:
                  self.leds_service.on('EarLeds')
              else:
                  self.leds_service.off('EarLeds')
      
          # Touch/distance sensors
      
          def startSensorMonitor(self):
              if self.sensorThread == None:
                  # create a thead that monitors directly the signal
                  self.sensorThread = threading.Thread(target = sensorThread, args = (self, ))
                  self.sensorThread.start()
                  time.sleep(0.5)
      
          def stopSensorMonitor(self):
              self.sensorThread.do_run = False
              self.sensorThread = None
      
          # Laser
      
          def startLaserMonitor(self):
              if self.laserThread==None:
                  #create a thead that monitors directly the signal
                  self.laserThread = threading.Thread(target = laserMonitorThread, args = (self.memory_service,))
                  self.laserThread.start()
      
          def stopLaserMonitor(self):
              self.laserThread.do_run = False
              self.laserThread = None
      
          # Camera
      
          def startFrameGrabber(self):
              # Connect to camera
              self.camProxy = ALProxy("ALVideoDevice", self.ip, self.port)
              resolution = 2    # VGA
              colorSpace = 11   # RGB
              # self.videoClient = self.camProxy.subscribe("grab3_images", resolution, colorSpace, 5)
              self.videoClient = self.camProxy.subscribeCamera("grab3_images", 0, resolution, colorSpace, 5)
              self.frame_grabber = True
      
          def stopFrameGrabber(self):
              # Connect to camera
              self.camProxy.unsubscribe(self.videoClient)
              self.frame_grabber = False
      
          def sendImage(self, ip, port):
              # Get a camera image.
              # image[6] contains the image data passed as an array of ASCII chars.
              img = self.camProxy.getImageRemote(self.videoClient)
      
              if img is None:
                  return 'ERROR'
      
              # Get the image size and pixel array.
              imageWidth = img[0]
              imageHeight = img[1]
              imageArray = img[6]
      
              # Create a PIL Image from our pixel array.
              imx = Image.frombytes("RGB", (imageWidth, imageHeight), imageArray)
      
              # Convert to grayscale
              img = imx.convert('L')
              aimg = img.tobytes()
      
              #print("Connecting to %s:%d ..." %(ip,port))
              try:
                  s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
                  s.connect((ip,port))
                  #print("OK")
      
                  #print("Sending image ...")
                  #print("Image size: %d %d" %(imageWidth * imageHeight, len(aimg)))
      
                  msg = '%9d\n' %len(aimg)
                  s.send(msg.encode())
                  s.send(aimg)
      
                  data = s.recv(80)
                  rcv_msg = data.decode()
                  #print("Reply: %s" %rcv_msg)
      
                  s.close()
                  #print("Connection closed ")
                  return rcv_msg
              except:
                  print("Send image: connection error")
                  return 'ERROR'
      
          def saveImage(self, filename):
      
              # Get a camera image.
              # image[6] contains the image data passed as an array of ASCII chars.
              img = self.camProxy.getImageRemote(self.videoClient)
      
              # Get the image size and pixel array.
              imageWidth = img[0]
              imageHeight = img[1]
              imageArray = img[6]
      
              # Create a PIL Image from our pixel array.
              imx = Image.frombytes("RGB", (imageWidth, imageHeight), imageArray)
      
              # Save the image.
              imx.save(filename, "PNG")
      
          def startFaceDetection(self):
              if self.face_detection:  # already active
                  return
      
              # Connect to camera
              self.startFrameGrabber()
      
              # Connect the event callback.
              self.frsub = self.memory_service.subscriber("FaceDetected")
              self.ch1 = self.frsub.signal.connect(self.on_facedetected)
              self.got_face = False
              self.savedfaces = []
              self.face_detection = True
              self.face_recording = False # if images are saved on file
      
          def stopFaceDetection(self):
              self.frsub.signal.disconnect(self.ch1)
              self.camProxy.unsubscribe(self.videoClient)
              self.face_recording = False
              self.face_detection = False
              self.white_eyes()
      
          def setFaceRecording(self,enable):
               self.face_recording = enable
      
          def on_facedetected(self, value):
              """
              Callback for event FaceDetected.
              """
              faceID = -1
      
              if value == []:  # empty value when the face disappears
                  self.got_face = False
                  self.facetimeStamp = None
                  self.white_eyes()
                  self.memset('facedetected', 'false')
              elif not self.got_face:  # only the first time a face appears
                  self.got_face = True
                  self.green_eyes()
                  self.memset('facedetected', 'true')
      
                  #print "I saw a face!"
                  #self.tts.say("Hello, you!")
                  self.facetimeStamp = time.time() #value[0]
                  #print "TimeStamp is: " + str(self.facetimeStamp)
      
                  # Second Field = array of face_Info's.
                  faceInfoArray = value[1]
                  for j in range( len(faceInfoArray)-1 ):
                      faceInfo = faceInfoArray[j]
      
                      # First Field = Shape info.
                      faceShapeInfo = faceInfo[0]
      
                      # Second Field = Extra info (empty for now).
                      faceExtraInfo = faceInfo[1]
      
                      faceID = faceExtraInfo[0]
      
                      #print "Face Infos :  alpha %.3f - beta %.3f" % (faceShapeInfo[1], faceShapeInfo[2])
                      #print "Face Infos :  width %.3f - height %.3f" % (faceShapeInfo[3], faceShapeInfo[4])
                      #print "Face Extra Infos :" + str(faceExtraInfo)
      
                      #print "Face ID: %d" %faceID
      
              if self.camProxy!=None and faceID>=0 and faceID not in self.savedfaces and self.face_recording:
                  # Get the image
                  img = self.camProxy.getImageRemote(self.videoClient)
      
                  # Get the image size and pixel array.
                  imageWidth = img[0]
                  imageHeight = img[1]
                  array = img[6]
      
                  # Create a PIL Image from our pixel array.
                  im = Image.frombytes("RGB", (imageWidth, imageHeight), array)
      
                  # Save the image.
                  fname = "face_%03d.png" %faceID
                  im.save(fname, "PNG")
                  print "Image face %d saved." %faceID
      
                  self.savedfaces.append(faceID)
      
          # Time of continuous face detection
          def faceDetectionTime(self):
              if self.facetimeStamp is not None:
                  return time.time() - self.facetimeStamp
              else:
                  return 0
      
          # Audio settings
      
          def getVolume(self):
              return self.audio_service.getOutputVolume()
      
          def setVolume(self, v):
              self.audio_service.setOutputVolume(v)
      
          def timestamp(self):
              return datetime.now().strftime("%Y%m%d_%H%M%S")
      
          # Audio recording
      
          def startAudioRecording(self):
              audiofile = '/home/nao/audio/audiorec_%s.wav' %(datetime.now().strftime("%Y%m%d_%H%M%S"))
      
              # Configures the channels that need to be recorded.
              channels = [0,0,1,0]  # Left, Right, Front, Rear
              self.audiorec_service.startMicrophonesRecording(audiofile, 'wav', 16000, channels)
              self.ears_led(True)
      
          def stopAudioRecording(self):
              self.audiorec_service.stopMicrophonesRecording()
              self.ears_led(False)
      
          # Speech
      
          # English, Italian, French
          def setLanguage(self, lang):
              languages = {"en" : "English", "it": "Italian"}
              if  (lang in languages.keys()):
                  lang = languages[lang]
              self.tts_service.setLanguage(lang)
      
          def tts(self, interaction):
              if self.stop_request:
                  return
              self.tts_service.setParameter("speed", 80)
              self.tts_service.say(interaction)
      
          def say(self, interaction):
              if self.stop_request:
                  return
              print('Say: %s' %interaction)
              if self.tts_service!=None:
                  self.tts_service.setParameter("speed", 80)
                  self.asay2(interaction)
      
          def asay2(self, interaction):
              if self.stop_request:
                  return
              if self.anspeech_service!=None:
                  # set the local configuration
                  configuration = {"bodyLanguageMode":"contextual"}
                  self.anspeech_service.say(interaction, configuration)
      
          def asay(self, interaction):
              if self.stop_request:
                  return
              if self.anspeech_service is None:
                  return
      
              # set the local configuration
              #configuration = {"bodyLanguageMode":"contextual"}
      
              # http://doc.aldebaran.com/2-5/naoqi/motion/alanimationplayer-advanced.html#animationplayer-list-behaviors-pepper
              vanim = ["animations/Stand/Gestures/Enthusiastic_4",
                       "animations/Stand/Gestures/Enthusiastic_5",
                       "animations/Stand/Gestures/Excited_1",
                       "animations/Stand/Gestures/Explain_1" ]
              anim = random.choice(vanim) # random animation
      
              if ('hello' in interaction):
                  anim = "animations/Stand/Gestures/Hey_1"
      
              self.anspeech_service.say("^start("+anim+") " + interaction+" ^wait("+anim+")")
      
          def reset_fake_asr(self):
              self.memory_service.insertData(self.fakeASRkey,'')
      
          def fake_asr(self):
              global asr_word, asr_confidence, asr_timestamp
              try:
                  r = self.memory_service.getData(self.fakeASRkey)
                  if r!='':
                      asr_word = r
                      asr_confidence = 1.0
                      asr_timestamp = self.memory_service.getData(self.fakeASRtimekey)
                      print('fake ASR: [%s], %r' %(asr_word,asr_timestamp))
                      self.reset_fake_asr()
              except:
                  pass
      
          def asr_cancel(self):
              self.asr_cancel_flag = True
      
          # vocabulary = list of keywords, e.g. ["yes", "no", "please"]
          # blocking until timeout
          def asr(self, vocabulary, timeout=5):
              global asr_word, asr_confidence, asr_timestamp
              #establishing vocabulary
              if (self.asr_service != None):
                  self.asr_service.pause(True)
                  self.asr_service.setVocabulary(vocabulary, False)
                  self.asr_service.pause(False)
                  # Start the speech recognition engine with user Test_ASR
                  self.asr_service.subscribe("asr_pepper_cmd")
                  print 'Speech recognition engine started'
      
                  #subscribe to event WordRecognized
                  subWordRecognized = self.memory_service.subscriber("WordRecognized")
                  idSubWordRecognized = subWordRecognized.signal.connect(onWordRecognized)
              else:
                  print('ASR service not available. Use %s memory key to say something' %self.fakeASRkey)
                  self.reset_fake_asr()
                  #val = raw_input('Enter ASR text: ')
                  #return val
      
              self.asr_cancel_flag = False
              asr_word = ''
              i = 0
              dt = 0.5
              while ((timeout<0 or i<timeout) and asr_word=='' and not self.asr_cancel_flag):
                  self.fake_asr()
                  time.sleep(dt)
                  i += dt
      
              if (self.asr_service != None):
                  #Disconnecting callbacks and subscribers
                  self.asr_service.unsubscribe("asr_pepper_cmd")
                  subWordRecognized.signal.disconnect(idSubWordRecognized)
      
              dt = time.time() - asr_timestamp
      
              print("dt %r %r  -  %f %f" %(time.time(),asr_timestamp, dt, timeout))
      
              if ((timeout<0 or dt<timeout) and asr_confidence>0.3):
                  print("ASR: %s" %asr_word)
                  return asr_word
              else:
                  print("ASR: none")
                  return ''
      
          def bip(self, r=1):
              print 'bip -- NOT IMPLEMENTED'
      
          def bop(self, r=1):
              print 'bop -- NOT IMPLEMENTED'
      
          # animations/Stand/Gestures/
          # Please_1
          # Hey_1; Hey_3; Hey_4
          def animation(self, interaction):
              if self.stop_request:
                  return
              if interaction[0:4]!='anim':
                  interaction = 'animations/Stand/Gestures/' + interaction
              print 'Animation ',interaction
              self.bm_service.setEnabled(False)
              self.ba_service.setEnabled(False)
              self.sm_service.setEnabled(False)
      
              try:
                  self.animation_player_service.run(interaction)
              except:
                  print("Error in executing gesture %s" %interaction)
      
              self.bm_service.setEnabled(self.alive)
              self.ba_service.setEnabled(self.alive)
              self.sm_service.setEnabled(self.alive)
      
          # Alive behaviors
      
          def setAlive(self, alive):
              if self.bm_service!=None:
                  self.alive = alive
                  print('Alive behaviors: %r' %self.alive)
                  self.bm_service.setEnabled(self.alive)
                  self.ba_service.setEnabled(self.alive)
                  self.sm_service.setEnabled(self.alive)
      
          # Tablet
      
          def showurl(self, weburl):
              if self.tablet_service!=None:
                  if weburl[0:4]!='http':
                      weburl = "http://198.18.0.1/apps/spqrel/%s" %(weburl)
                  print("URL: %s" %weburl)
                  if weburl[-3:]=='jpg' or weburl[-3:]=='png':
                      self.tablet_service.showImage(weburl)
                  else:
                      self.tablet_service.showWebview(weburl)
      
          # Robot motion
      
          def stop(self):
              print 'stop'
              self.motion_service.stopMove()
              if self.beh_service!=None:
                  bns = self.beh_service.getRunningBehaviors()
                  for b in bns:
                      self.beh_service.stopBehavior(b)
      
          def forward(self, r=1):
              if self.stop_request:
                  return
              print 'forward',r
              x = r
              y = 0.0
              theta = 0.0
              self.motion_service.moveTo(x, y, theta) #blocking function
      
          def backward(self, r=1):
              if self.stop_request:
                  return
              print 'backward',r
              x = -r
              y = 0.0
              theta = 0.0
              self.motion_service.moveTo(x, y, theta) #blocking function
      
          def left(self, r=1):
              if self.stop_request:
                  return
              print 'left',r
              #Turn 90deg to the left
              x = 0.0
              y = 0.0
              theta = math.pi/2 * r
              self.motion_service.moveTo(x, y, theta) #blocking function
      
          def right(self, r=1):
              if self.stop_request:
                  return
              print 'right',r
              #Turn 90deg to the right
              x = 0.0
              y = 0.0
              theta = -math.pi/2 * r
              self.motion_service.moveTo(x, y, theta) #blocking function
      
          def turn(self, r):
              if self.stop_request:
                  return
              print 'turn',r
              #Turn r deg
              vx = 0.0
              vy = 0.0
              vth = r * math.pi / 180
              self.motion_service.moveTo(vx, vy, vth) #blocking function
      
          def setSpeed(self,vx,vy,vth,tm,stopOnEnd=False):
              if self.stop_request:
                  return
              self.motion_service.move(vx, vy, vth)
              time.sleep(tm)
              if stopOnEnd:
                  self.motion_service.move(0, 0, 0)
                  self.motion_service.stopMove()
      
          # Head motion
      
          def headPose(self, yaw, pitch, tm):
              jointNames = ["HeadYaw", "HeadPitch"]
              initAngles = [yaw, pitch]
              timeLists  = [tm, tm]
              isAbsolute = True
              self.motion_service.angleInterpolation(jointNames, initAngles, timeLists, isAbsolute)
      
          def headscan(self):
              jointNames = ["HeadYaw", "HeadPitch"]
              # look left
              initAngles = [1.6, -0.2]
              timeLists  = [5.0, 5.0]
              isAbsolute = True
              self.motion_service.angleInterpolation(jointNames, initAngles, timeLists, isAbsolute)
              # look right
              finalAngles = [-1.6, -0.2]
              timeLists  = [10.0, 10.0]
              self.motion_service.angleInterpolation(jointNames, finalAngles, timeLists, isAbsolute)
              # look ahead center
              finalAngles = [0.0, -0.2]
              timeLists  = [5.0, 5.0]
              self.motion_service.angleInterpolation(jointNames, finalAngles, timeLists, isAbsolute)
      
          # Arms stiffness [0,1]
          def setArmsStiffness(self, stiff_arms):
              names = "LArm"
              stiffnessLists = stiff_arms
              timeLists = 1.0
              self.motion_service.stiffnessInterpolation(names, stiffnessLists, timeLists)
              names = "RArm"
              self.motion_service.stiffnessInterpolation(names, stiffnessLists, timeLists)
      
          # Wait
      
          def wait(self, r=1):
              print 'wait',r
              for i in range(0,r):
                  time.sleep(3)
      
          # Sensors
      
          def sensorvalue(self, sensorname='all'):
              if (sensorname == 'frontsonar'):
                  return self.sonar[0]
              elif (sensorname == 'rearsonar'):
                  return self.sonar[1]
              elif (sensorname == 'headtouch'):
                  return self.headTouch
              elif (sensorname == 'lefthandtouch'):
                  return self.handTouch[0]
              elif (sensorname == 'righthandtouch'):
                  return self.handTouch[1]
              elif (sensorname == 'frontlaser'):
                  return self.frontlaser
              elif (sensorname == 'all'):
                  return [self.frontlaser,  self.sonar[0],  self.sonar[1],
                      self.headTouch, self.handTouch[0], self.handTouch[1] ]
      
          def sensorvaluestring(self):
              return '%.1f,%.1f,%.1f,%d,%d,%d' %(self.sensorvalue('frontlaser'),self.sensorvalue('frontsonar'),self.sensorvalue('rearsonar'),self.sensorvalue('headtouch'),self.sensorvalue('lefthandtouch'),self.sensorvalue('righthandtouch'))
      
          # Behaviors
      
          def normalPosture(self):
              jointValues = [0.00, -0.21, 1.55, 0.13, -1.24, -0.52, 0.01, 1.56, -0.14, 1.22, 0.52, -0.01,
                             0, 0, 0, 0, 0]
              isAbsolute = True
              self.motion_service.angleInterpolation(self.jointNames, jointValues, 3.0, isAbsolute)
      
          def setPosture(self, jointValues):
              isAbsolute = True
              self.motion_service.angleInterpolation(self.jointNames, jointValues, 3.0, isAbsolute)
      
          def getPosture(self):
              pose = None
              useSensors = True
              pose = self.motion_service.getAngles(self.jointNames, useSensors)
              return pose
      
          def raiseArm(self, which='R'): # or 'R'/'L' for right/left arm
              if (which=='R'):
                  jointNames = ["RShoulderPitch", "RShoulderRoll", "RElbowYaw", "RElbowRoll", "RWristYaw"]
                  jointValues = [ -1.0, -0.3, 1.22, 0.52, -1.08]
              else:
                  jointNames = ["LShoulderPitch", "LShoulderRoll", "LElbowYaw", "LElbowRoll", "LWristYaw"]
                  jointValues = [ -1.0, 0.3, -1.22, -0.52, 1.08]
      
              isAbsolute = True
              self.motion_service.angleInterpolation(jointNames, jointValues, 3.0, isAbsolute)
      
          def stand(self):
              if self.al_service.getState()!='disabled':
                  self.al_service.setState('disabled')
              self.rp_service.goToPosture("Stand",2.0)
      
          def disabled(self):
              #self.tts_service.say("Bye bye")
              self.al_service.setState('disabled')
      
          def interactive(self):
              #tts_service.say("Interactive")
              self.al_service.setState('interactive')
      
          def run_behavior(self, bname):
              if self.beh_service!=None:
                  try:
                      self.beh_service.startBehavior(bname)
                      #time.sleep(10)
                      #self.beh_service.stopBehavior(bname)
                  except:
                      pass
      
          def sax(self):
              str = 'sax'
              print(str)
              bname = 'saxophone-0635af/behavior_1'
              self.run_behavior(bname)
      
          def dance(self):
              str = 'dance'
              print(str)
              bname = 'dance/behavior_1'
              self.run_behavior(bname)
      
          def takephoto(self):
              str = 'take photo'
              print(str)
              #tts_service.say("Cheers")
              bname = 'takepicture-61492b/behavior_1'
              self.run_behavior(bname)
      
          def introduction(self):
              str = 'introduction'
              print(str)
              bname = 'animated-say-5b866d/behavior_1'
              self.run_behavior(bname)
      
          # People
      
          def getPeopleInfo(self):
              pl = self.memory_service.getData('PeoplePerception/PeopleList')
                  # PeoplePerception/PeopleList
              print('People list: %s' %str(pl))
              r = []
              for id in pl:
                  print('Person ID %d' %id)
                  gekey = 'PeoplePerception/Person/%d/GenderProperties' %id
                  smkey = 'PeoplePerception/Person/%d/SmileProperties' %id
                  agkey = 'PeoplePerception/Person/%d/AgeProperties' %id
      
                  ge = self.memory_service.getData(gekey)  # 0 female, 1 male, confidence
                  sm = self.memory_service.getData(smkey)  # 0-1 smile, confidence
                  ag = self.memory_service.getData(agkey)  # age, confidence
                  d = {}
                  d['gender'] = ge
                  d['age'] = ag
                  d['smile'] = sm
                  r.append(d)
                  #rstr= "gender: %s, age: %s, smile: %s" %(str(ge),str(ag),str(sm))
                  #print(rtrs)
              return r
      
          # Battery
      
          def getBatteryCharge(self):
              return self.battery_service.getBatteryCharge()
      
          # Memory
      
          def memset(self, key, val):
              self.memory_service.insertData(key,val)
      
          def memget(self, key):
              try:
                  return self.memory_service.getData(key)
              except:
                  return ''
      
          # Logging functions
      
          def logenable(self,enable=True):
              if enable:
                  if (self.logfile is None):
                      timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
                      logfilename = '/tmp/pepper_%s.log' %timestamp
                      self.logfile = open(logfilename,'a')
                      self.lthr = threading.Thread(target = self.logthread)
                      self.lthr.start()
                      print('Log enabled on file %s.' %logfilename)
              else:
                  if (self.logfile is not None):
                      self.logclose()
                      self.lthr.do_run = False
                      self.lthr = None
                      print('Log disabled.')
      
          def logdata(self, data):
              if (self.logfile is not None):
                  timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
                  self.logfile.write("%s;%r\n" %(timestamp, data))
                  self.logfile.flush()
      
          def logclose(self):
              if (self.logfile != None):
                  self.logfile.close()
                  self.logfile = None
      
          def logthread(self):
              t = threading.currentThread()
              while getattr(t, "do_run", True):
                  try:
                      z = self.getState()
                      self.logdata(z)
                  except:
                      pass
                  time.sleep(1)
      
          def setFERserver(self,ip,port=5678):
              self.FER_server_IP = ip
              self.FER_server_port = port
      
          def getState(self):
              # v1
              # frontlaser, frontsonar, backsonar, headtouch, lefthandtouch,
              # righthandtouch, screenx, screeny, face, happy
              # v2
              # frontlaser, frontsonar, backsonar, headtouch, lefthandtouch,
              # righthandtouch, head_yaw, head_pitch, screenx, screeny, touchcnt, face, happy
      
              z = self.sensorvalue() # frontlaser...handtouch
              useSensors = True
              headPose = self.motion_service.getAngles(["HeadYaw", "HeadPitch"],
                                                       useSensors)
              z.append(headPose[0])
              z.append(headPose[1])
              z.append(self.screenTouch[0])
              z.append(self.screenTouch[1])
              z.append(touchcnt)
              z.append(1.0 if self.got_face else 0.0)
              if self.FER_server_IP is not None:
                  r = self.sendImage(self.FER_server_IP,self.FER_server_port)
              else:
                  r = None
              v = []
              if r is not None and type(r)!=type('str'):
                  v = eval(r)
              h = 0.0
              for c in v:
                  h = max(h,c[1])
              z.append(h)
              return z
      
      ```
      </details>

    * `pepper_cmd_server.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #!/usr/bin/env python
      
      import sys
      import os
      import socket
      import importlib
      from threading import Thread
      
      import re
      import argparse
      import qi
      import pepper_cmd
      from pepper_cmd import *
      
      status = "Idle"             # robot status sent to websocket
      
      robot = pepper_cmd.robot
      
      def exec_fn(fn,vd):
          largs = []
          for i in range(1,len(vd)):
              if (vd[i]!=''):
                  largs += [ vd[i] ]
          print 'Executing ',fn,' with args ',largs
          try:
              if (len(largs)==0):
                  fn()
              elif (len(largs)==1):
                  if (largs[0][0]=='"'):
                      print "string argument"
                      fn(largs[0])
                  else:
                      fn(int(largs[0]))
              elif (len(largs)==2):
                  fn(int(largs[0]),int(largs[1]))
          except:
              print "ERROR: executing",fn," with args ",largs
      
      def exec_cmd(data):
          print "received command:", data
          vd = re.split('[(,)_]',data)
          try:
              fn = getattr(pepper_cmd, vd[0])
          except:
              print "ERROR: function",vd[0],"not found"
              fn = None
          if (not fn is None):
              exec_fn(fn,vd)
      
      def run_code(code):
          global status
          if (code is None):
              return
          print("=== Start code run ===")
          #code = beginend(code)
          print("Executing")
          print(code)
          try:
              status = "Executing program"
              exec(code)
          except Exception as e:
              print("CODE EXECUTION ERROR")
              print e
          status = "Idle"
          print("=== End code run ===")
      
      def start_server(TCP_PORT):
      
          TCP_IP = ''
          BUFFER_SIZE = 200
      
          s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
          s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
          s.bind((TCP_IP,TCP_PORT))
          s.listen(1)
      
          run=True
      
          while run:
              print "Robot Program Server: waiting for connections port", TCP_PORT
              connected = False
              conn = None
              try:
                  conn, addr = s.accept()
                  print "Connection address:", addr
                  connected = True
              except KeyboardInterrupt:
                  print "User quit."
                  run = False
      
              while (run and connected):
                  data = ''
                  while (run and connected and ((data=='') or (data[0]!='*' and data[0]!='[' and (not '###ooo###' in data)))):
                      try:
                          d = conn.recv(BUFFER_SIZE)
                      except:
                          print "Pepper Cmd Server: connection closed."
                          connected = False
                          break
                      if (d==''):
                          break
                      data = data + d
                      #print "Received partial data: ",data
      
                  if (not connected):
                      break
                  if (not data or data==''):
                      break
      
                  print "Received: ",data
                  conn.send("OK\n")
      
                  new_version = True
                  if (new_version):
                      if (status=='Idle'):
                          t = Thread(target=run_code, args=(data,))
                          t.start()
                  else:
                      # old version
                      vdata = re.split("[\r\n;]",data)
                      for i in range(0,len(vdata)):
                          if (vdata[i]=="quit"):
                              connected=False
                              break
                          else:
                              com = vdata[i].strip()
                              if (len(com)>0):
                                  exec_cmd(com)
      
              if (conn is not None):
                  conn.close()
              print "Closed connection"
      
      def main():
          parser = argparse.ArgumentParser()
          #parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
          #                    help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          #parser.add_argument("--pport", type=int, default=9559,
          #                    help="Naoqi port number")
          parser.add_argument("--serverport", type=int, default=5000,
                              help="Server port")
      
          args = parser.parse_args()
          #pip = args.pip
          #pport = args.pport
          #server_port = args.serverport
      
          #Starting application
          #try:
          #    connection_url = "tcp://" + pip + ":" + str(pport)
          #    app = qi.Application(["Program server", "--qi-url=" + connection_url ])
          #except RuntimeError:
          #    print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
          #           "Please check your script arguments. Run with -h option for help.")
          #    sys.exit(1)
      
          #app.start()
          #pepper_cmd.session = app.session
      
          start_server(args.serverport)
      
      if __name__ == "__main__":
          main()
      
      ```
      </details>

    * `pepper_send_program.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #!/usr/bin/env python
      
      import sys
      import os
      import socket
      import importlib
      import re
      import argparse
      import qi
      
      def start_client(server_ip,server_port,program):
      
          TCP_IP = ''
          BUFFER_SIZE = 200
      
          print "Connecting to %s:%d..." %(server_ip,server_port)
      
          s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
      
          s.connect((server_ip,server_port))
      
          f = open(program,'r')
          data = f.read()
          f.close()
      
          print "Sending program...",
      
          s.send(data+ "\n###ooo###\n")
      
          print(" done")
      
          data = s.recv(BUFFER_SIZE)
      
          print "Reply: ",data
      
          s.close()
      
          print("Closed connection")
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--serverip", type=str, default='127.0.0.1',
                              help="Server IP address.")
          parser.add_argument("--serverport", type=int, default=5000,
                              help="Server port")
          parser.add_argument("--program", type=str, default="default.py",
                              help="Program file to send")
      
          args = parser.parse_args()
          server_ip = args.serverip
          server_port = args.serverport
          program = args.program
      
          #Starting application
          start_client(server_ip,server_port,program)
      
      if __name__ == "__main__":
          main()
      
      ```
      </details>

    * `test.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      import pepper_cmd
      from pepper_cmd import *
      
      begin()
      
      pepper_cmd.robot.setAlive(True)
      pepper_cmd.robot.showurl('index.html')
      pepper_cmd.robot.setLanguage('Italian')
      pepper_cmd.robot.setVolume(50)
      pepper_cmd.robot.say('Ciao.')
      
      end()
      
      ```
      </details>

    * `websocket_pepper.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      # http://www.html.it/pag/53419/websocket-server-con-python/
      # sudo -H easy_install tornado
      
      import tornado.httpserver
      import tornado.websocket
      import tornado.ioloop
      import tornado.web
      import socket
      import time
      import argparse
      import qi
      from threading import Thread
      
      #from dummy_robot import begin,end,forward,backward,left,right
      
      #import sys
      #sys.path.append('../program')
      
      import pepper_cmd
      from pepper_cmd import *
      
      # Global variables
      
      websocket_server = None     # websocket handler
      run = True                  # main_loop run flag
      server_port = 9020          # web server port
      code = None
      status = "Idle"             # robot status sent to websocket
      
      session = None
      tablet_service = None
      webview = "http://198.18.0.1/apps/spqrel/index.html"
      
      RED   = "\033[1;31m"
      BLUE  = "\033[1;34m"
      CYAN  = "\033[1;36m"
      GREEN = "\033[0;32m"
      RESET = "\033[0;0m"
      BOLD    = "\033[;1m"
      REVERSE = "\033[;7m"
      
      # Websocket server handler
      
      class MyWebSocketServer(tornado.websocket.WebSocketHandler):
      
          def open(self):
              global websocket_server, run
              websocket_server = self
              print('New connection')
      
          def on_message(self, message):
              global code, status
              if (message=='stop'):
                  print('Stop code and robot')
                  robot_stop_request()
              else:
                  print('Code received:\n%s' % message)
                  if (status=='Idle'):
                      t = Thread(target=run_code, args=(message,))
                      t.start()
                  else:
                      print('Program running. This code is discarded.')
              self.write_message('OK')
      
          def on_close(self):
              print('Connection closed')
      
          def on_ping(self, data):
              print('ping received: %s' %(data))
      
          def on_pong(self, data):
              print('pong received: %s' %(data))
      
          def check_origin(self, origin):
              #print("-- Request from %s" %(origin))
              return True
      
      # Touchscreen callback
      
      # function called when the signal onTouchDown is triggered
      def onTouched(x, y):
          global session,tablet_service
          print "coordinates are x: ", x, " y: ", y
          al_service = session.service("ALAutonomousLife")
          if (al_service.getState()!='disabled'):
              tablet_service.showWebview(webview)
      
      # Hand touch callback
      
      def rhTouched(value):
          global session,tablet_service
          print "Right Hand value subscriber=",value
          #al_service = session.service("ALAutonomousLife")
          #if (al_service.getState()!='disabled'):
          if value==1.0:
              tablet_service.showWebview(webview)
      
      # Main loop (asynchrounous thread)
      
      def main_loop(data):
          global run, websocket_server, status, tablet_service
          while (run):
              time.sleep(1)
              #if (run and not websocket_server is None):
                  #try:
                      #websocket_server.write_message(status)
                      #print(status)
                  #except tornado.websocket.WebSocketClosedError:
                      #print('Connection closed.')
                      #websocket_server = None
      
          print("Main loop quit.")
      
      def run_code(code):
          global status
          if (code is None):
              return
          print("=== Start code run ===")
          #code = beginend(code)
          print("Executing")
          print(code)
          try:
              status = "Executing program"
              exec(code)
          except Exception as e:
              print("CODE EXECUTION ERROR")
              print e
          status = "Idle"
          print("=== End code run ===")
      
      # Main program
      
      def main():
          global run
      
          # Run main thread
          t = Thread(target=main_loop, args=(None,))
          t.start()
      
          # Run robot
          begin()
      
          # Run web server
          application = tornado.web.Application([
              (r'/websocketserver', MyWebSocketServer),])
          http_server = tornado.httpserver.HTTPServer(application)
          http_server.listen(server_port)
          print("%sWebsocket server listening on port %d%s" %(GREEN,server_port,RESET))
      
          try:
              tornado.ioloop.IOLoop.instance().start()
          except KeyboardInterrupt:
              print(" -- Keyboard interrupt --")
      
          # Quit
          end()
      
          if (not websocket_server is None):
              websocket_server.close()
          print("Web server quit.")
          run = False
          print("Waiting for main loop to quit...")
      
      def main_OLD():
          global run,session,tablet_service
      
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--serverport", type=int, default=9000,
                              help="Server port")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          server_port = args.serverport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["Websocket server", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
          pepper_cmd.session = app.session
          tablet_service = app.session.service("ALTabletService")
      
          memory_service  = session.service("ALMemory")
      
          #Tablet touch (does not forward signal to other layers)
          #idTTouch = tablet_service.onTouchDown.connect(onTouched)
      
          #subscribe to any change on "HandRightBack" touch sensor
          rhTouch = memory_service.subscriber("HandRightBackTouched")
          idRHTouch = rhTouch.signal.connect(rhTouched)
      
          # Run main thread
          t = Thread(target=main_loop, args=(None,))
          t.start()
      
          # Run robot
          begin()
      
          # Run web server
          application = tornado.web.Application([
              (r'/websocketserver', MyWebSocketServer),])
          http_server = tornado.httpserver.HTTPServer(application)
          http_server.listen(server_port)
          print("%sWebsocket server listening on port %d%s" %(GREEN,server_port,RESET))
      #    tablet_service.showWebview(webview)
      
          try:
              tornado.ioloop.IOLoop.instance().start()
          except KeyboardInterrupt:
              print(" -- Keyboard interrupt --")
      
          # Quit
          end()
      
          if (not websocket_server is None):
              websocket_server.close()
          print("Web server quit.")
          run = False
          print("Waiting for main loop to quit...")
      
      if __name__ == "__main__":
          main()
      
      ```
      </details>

  * **demo/**
    * `sapientino.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/core/altabletservice-api.html
      
      import qi
      import argparse
      import sys
      import os
      import math
      import functools
      
      commands = [ ['F', 620, 130], ['B', 620, 500], ['L', 480, 310], ['R', 820, 310],
      ['OK', 620, 310], ['X', 480, 500], ['A', 820, 500] ]
      
      tts_service = None
      motion_service = None
      
      plan = []
      flag_run = False
      stop_plan = True
      
      def reset():
          global plan, flag_run
          plan = []
          flag_run = False
          moveHead(0,-0.3,1.0)
          motion_service.stopMove()
      
      def moveHead(yaw, pitch, headtime):
          global motion_service
          jointsNames = ["HeadYaw", "HeadPitch"]
          # we move head to center
          #print "Moving head to ", yaw, pitch
          finalAngles = [yaw, pitch]
          timeLists  = [headtime, headtime]
          isAbsolute = True
          motion_service.angleInterpolation(jointsNames, finalAngles, timeLists, isAbsolute)
      
      def forward(r=1):
          print 'forward',r
          #Move in its X direction
          x = r * 0.5
          y = 0.0
          theta = 0.0
          motion_service.moveTo(x, y, theta) #blocking function
      
      def backward(r=1):
          print 'backward',r
          x = -r * 0.5
          y = 0.0
          theta = 0.0
          motion_service.moveTo(x, y, theta) #blocking function
      
      def left(r=1):
          print 'left',r
          #Turn 90deg to the left
          x = 0.0
          y = 0.0
          theta = math.pi/2 * r
          motion_service.moveTo(x, y, theta) #blocking function
      
      def right(r=1):
          print 'right',r
          #Turn 90deg to the right
          x = 0.0
          y = 0.0
          theta = -math.pi/2 * r
          motion_service.moveTo(x, y, theta) #blocking function
      
      def say_command(cmd):
          global tts_service
          print cmd
          if (cmd[0]=='F'):
              strsay = "Avanti"
          elif (cmd[0]=='B'):
              strsay = "Indietro"
          elif (cmd[0]=='L'):
              strsay = "Sinistra"
          elif (cmd[0]=='R'):
              strsay = "Destra"
          elif (cmd[0]=='OK'):
              strsay = "OK"
          elif (cmd[0]=='X'):
              strsay = "Missione cancellata. Riprova."
          elif (cmd[0]=='A'):
              strsay = "Azione"
          if (cmd[1]>1):
              strsay += " %d volte" %(cmd[1])
      
          tts_service.say(strsay)
      
      def say_plan(plan):
          global tts_service
          tts_service.say("Hai programmato la missione: ")
          for p in plan:
              say_command(p)
          tts_service.say("premi di nuovo il tasto OK per partire")
      
      def compact_plan(plan):
          splan = []
          last = ' '
          i=-1
          for a in plan:
              if (a==last):
                  splan[i][1] += 1
              else:
                  splan.append([a,1])
                  i += 1
              last = a
          return splan
      
      def exec_command(cmd):
          global tts_service, stop_plan
          if (stop_plan):
              return
          say_command(cmd)
          if (cmd[0]=='F'):
              forward(cmd[1])
          if (cmd[0]=='B'):
              backward(cmd[1])
          if (cmd[0]=='L'):
              left(cmd[1])
          if (cmd[0]=='R'):
              right(cmd[1])
      
      def exec_plan(plan):
          global tts_service, stop_plan
          print "Execution ",plan
          tts_service.say("Missione avviata!")
          stop_plan = False
          for a in plan:
              exec_command(a)
          tts_service.say("Missione compiuta. Pronto per una nuova missione.")
          reset()
      
      # function called when the signal onTouchDown is triggered
      def onTouched(x, y):
          global tts_service
          global plan, flag_run
      
          print "coordinates are x: ", x, " y: ", y
          mind=200
          cmd = ''
          for a in commands:
              d = abs(x - a[1]) + abs(y - a[2])
              if (d < mind):
                  mind = d
                  cmd = a[0]
          if (cmd!=''):
              print 'Sapientino key: ',cmd
              scmd = [cmd,1]
              say_command(scmd)
              if (cmd=='X'):
                  reset()
              elif (cmd=='OK'):
                  splan = compact_plan(plan)
                  if (flag_run):
                      exec_plan(splan)
                  else:
                      say_plan(splan)
                      flag_run = True
              else:
      	    plan.append(cmd)
      	    #print plan
      
      def onHeadTouched(motion_service, value):
          global stop_plan
          motion_service.stopMove()
          stop_plan = True
      
      def main():
          global tts_service, motion_service
      
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["TabletModule", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          memory_service = session.service("ALMemory")
          motion_service = session.service("ALMotion")
          tablet_service = session.service("ALTabletService")
      
          tablet_service.showImage("http://198.18.0.1/apps/spqrel/img/sapdoc-buttons.jpg")
      
          motion_service.setExternalCollisionProtectionEnabled("Move", False)
      
          tts_service = session.service("ALTextToSpeech")
          tts_service.setLanguage("Italian")
      
          #subscribe to any change on any touch sensor
          anyTouch = memory_service.subscriber("TouchChanged")
          idAnyTouch = anyTouch.signal.connect((functools.partial(onHeadTouched, motion_service)))
      
          moveHead(0,-0.3,1.0)
      
          tts_service.say("ciao, sono il tuo amico Peppino.")
          tts_service.say("pronto per la programmazione.")
      
          idTTouch = tablet_service.onTouchDown.connect(onTouched)
          app.run()
          motion_service.setExternalCollisionProtectionEnabled("Move", True)
      
          anyTouch.signal.disconnect(idAnyTouch)
      
      if __name__ == "__main__":
          main()
      ```
      </details>

  * **faces/**
    * `vision_faceDetection.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #! /usr/bin/env python
      # -*- encoding: UTF-8 -*-
      
      """Example: A Simple class to get & read FaceDetected Events"""
      
      import qi
      import time
      import sys
      import os
      import argparse
      
      # Python Image Library
      import Image
      
      from naoqi import ALProxy
      
      class HumanGreeter(object):
          """
          A simple class to react to face detection events.
          """
      
          def __init__(self, app):
              """
              Initialisation of qi framework and event detection.
              """
              super(HumanGreeter, self).__init__()
              app.start()
              session = app.session
              # Get the service ALMemory.
              self.memory = session.service("ALMemory")
              self.ba_service = session.service("ALBasicAwareness")
              self.ba_service.setEnabled(True)
              self.leds_service = session.service("ALLeds")
              self.leds_service.on('FaceLeds') # reset to white
              # Connect the event callback.
              self.fdsub = self.memory.subscriber("FaceDetected")
              self.ch1 = self.fdsub.signal.connect(self.on_human_tracked)
              # Get the services ALTextToSpeech and ALFaceDetection.
              self.tts = session.service("ALTextToSpeech")
              self.face_detection = session.service("ALFaceDetection")
              self.face_detection.subscribe("HumanGreeter")
              self.got_face = False
              self.camProxy = None
              self.savedfaces = []
      
          def connect_camera(self, ip, port):
              # Connect to camera
              self.camProxy = ALProxy("ALVideoDevice", ip, port)
              resolution = 2    # VGA
              colorSpace = 11   # RGB
              self.videoClient = self.camProxy.subscribe("vision_faceDetection", resolution, colorSpace, 5)
      
          def on_human_tracked(self, value):
              """
              Callback for event FaceDetected.
              """
              faceID = -1
      
              if value == []:  # empty value when the face disappears
                  self.got_face = False
                  # white face leds
                  self.leds_service.on('FaceLeds')
      
              else:
                  # green face leds
                  self.leds_service.off('LeftFaceLedsRed')
                  self.leds_service.off('LeftFaceLedsBlue')
                  self.leds_service.off('RightFaceLedsRed')
                  self.leds_service.off('RightFaceLedsBlue')
      
                  if not self.got_face:  # only the first time a face appears
      
                      self.got_face = True
                      print "I saw a face!"
                      #self.tts.say("Hello, you!")
                      # First Field = TimeStamp.
                      timeStamp = value[0]
                      print "TimeStamp is: " + str(timeStamp)
      
                      # Second Field = array of face_Info's.
                      faceInfoArray = value[1]
                      for j in range( len(faceInfoArray)-1 ):
                          faceInfo = faceInfoArray[j]
      
                          # First Field = Shape info.
                          faceShapeInfo = faceInfo[0]
      
                          # Second Field = Extra info (empty for now).
                          faceExtraInfo = faceInfo[1]
      
                          faceID = faceExtraInfo[0]
      
                          #print "Face Infos :  alpha %.3f - beta %.3f" % (faceShapeInfo[1], faceShapeInfo[2])
                          #print "Face Infos :  width %.3f - height %.3f" % (faceShapeInfo[3], faceShapeInfo[4])
                          #print "Face Extra Infos :" + str(faceExtraInfo)
                          #print "Face ID: %d" %faceID
      
              if self.camProxy!=None and faceID>=0 and faceID not in self.savedfaces:
                  # Get the image
                  img = self.camProxy.getImageRemote(self.videoClient)
      
                  # Get the image size and pixel array.
                  imageWidth = img[0]
                  imageHeight = img[1]
                  array = img[6]
      
                  # Create a PIL Image from our pixel array.
                  im = Image.frombytes("RGB", (imageWidth, imageHeight), array)
      
                  # Save the image.
                  fname = "face_%03d.png" %faceID
                  im.save(fname, "PNG")
                  print "Image face %d saved." %faceID
      
                  self.savedfaces.append(faceID)
      
          def close(self):
              self.face_detection.unsubscribe("HumanGreeter")
              self.camProxy.unsubscribe(self.videoClient)
              self.fdsub.signal.disconnect(self.ch1)
              self.ba_service.setEnabled(False)
              self.leds_service.on('FaceLeds') # reset to white
      
          def run(self):
              """
              Loop on, wait for events until manual interruption.
              """
              print "Starting HumanGreeter"
              try:
                  while True:
                      #val = self.memory.getData('FaceDetection/FaceDetected')
                      #if len(val)>0:
                      #    print('Memory value %r' %val)
                      time.sleep(1)
              except KeyboardInterrupt:
                  print "Interrupted by user, stopping all behaviors"
                  self.close()
                  sys.exit(0)
      
      if __name__ == "__main__":
          parser = argparse.ArgumentParser()
          parser.add_argument("--ip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address. On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--port", type=int, default=9559,
                              help="Naoqi port number")
      
          args = parser.parse_args()
          try:
              # Initialize qi framework.
              connection_url = "tcp://" + args.ip + ":" + str(args.port)
              app = qi.Application(["HumanGreeter", "--qi-url=" + connection_url])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + args.ip + "\" on port " + str(args.port) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          human_greeter = HumanGreeter(app)
          human_greeter.connect_camera(args.ip, args.port)
          human_greeter.run()
      
      ```
      </details>

  * **grab_image/**
    * **getimages/**
    * `record_video.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #!/usr/bin/env python
      
      # -*- encoding: UTF-8 -*-
      #
      # This example demonstrates how to use the ALVideoRecorder module to record a
      # video file on the robot.
      #
      # Usage: python vision_videorecord.py "robot_ip"
      #
      
      import os
      import sys
      import argparse
      import time
      from naoqi import ALProxy
      
      if __name__ == "__main__":
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--camera", type=int, default=0,
                              help="Robot camera ID address. 0 by default")
      
          args = parser.parse_args()
          IP = args.pip
          PORT = args.pport
      
          videoRecorderProxy = ALProxy("ALVideoRecorder", IP, PORT)
      
          # This records a 320*240 MJPG video at 10 fps.
          # Note MJPG can't be recorded with a framerate lower than 3 fps.
          videoRecorderProxy.setResolution(1)
          videoRecorderProxy.setFrameRate(10)
          videoRecorderProxy.setVideoFormat("MJPG")
          videoRecorderProxy.startRecording("/home/nao/recordings/cameras", "myvideo")
      
          time.sleep(5)
      
          # Video file is saved on the robot in the
          # /home/nao/recordings/cameras/ folder.
          videoInfo = videoRecorderProxy.stopRecording()
      
          print "Video was saved on the robot: ", videoInfo[1]
          print "Num frames: ", videoInfo[0]
      ```
      </details>

    * `save_image.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #!/usr/bin/env python
      
      # -*- encoding: UTF-8 -*-
      # Get an image from NAO. Display it and save it using PIL.
      import os
      import sys
      import time
      
      import argparse
      
      # Python Image Library  - pip install Pillow ???
      from PIL import Image
      
      from naoqi import ALProxy
      
      def showNaoImage(IP, PORT, camID):
        """
        First get an image from Nao, then show it on the screen with PIL.
        """
      
        camProxy = ALProxy("ALVideoDevice", IP, PORT)
        resolution = 2    # VGA
        colorSpace = 11   # RGB
      
        videoClient = camProxy.subscribeCamera("python_client", camID, resolution, colorSpace, 5)
      
        t0 = time.time()
      
        # Get a camera image.
        # image[6] contains the image data passed as an array of ASCII chars.
        naoImage = camProxy.getImageRemote(videoClient)
      
        t1 = time.time()
      
        # Time the image transfer.
        print "acquisition delay ", t1 - t0
      
        camProxy.unsubscribe(videoClient)
      
        # Now we work with the image returned and save it as a PNG  using ImageDraw
        # package.
      
        # Get the image size and pixel array.
        imageWidth = naoImage[0]
        imageHeight = naoImage[1]
        array = naoImage[6]
      
        # Create a PIL Image from our pixel array.
        im = Image.frombytes("RGB", (imageWidth, imageHeight), array)
      
        # Save the image.
        im.save("camImage.png", "PNG")
      
        im.show()
      
      if __name__ == '__main__':
        parser = argparse.ArgumentParser()
        parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                          help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
        parser.add_argument("--pport", type=int, default=9559,
                          help="Naoqi port number")
        parser.add_argument("--camera", type=int, default=0,
                          help="Robot camera ID address. 0 by default")
      
        args = parser.parse_args()
        IP = args.pip
        PORT = args.pport
        camID = args.camera
      
        naoImage = showNaoImage(IP, PORT, camID)
      
      ```
      </details>

    * `view_image.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #!/usr/bin/env python
      
      # -*- encoding: UTF-8 -*-
      #
      # This is a tiny example that shows how to show live images from Nao using PyQt.
      # You must have python-qt4 installed on your system.
      #
      
      import os
      import sys
      import argparse
      
      from PyQt5.QtGui import QImage, QPainter
      from PyQt5.QtWidgets import QApplication, QWidget
      
      from naoqi import ALProxy
      
      # To get the constants relative to the video.
      import vision_definitions
      
      class ImageWidget(QWidget):
          """
          Tiny widget to display camera images from Naoqi.
          """
          def __init__(self, IP, PORT, CameraID, parent=None):
              """
              Initialization.
              """
              QWidget.__init__(self, parent)
              self._image = QImage()
              self.setWindowTitle('Pepper')
      
              self._imgWidth = 320
              self._imgHeight = 240
              self._cameraID = CameraID
              self.resize(self._imgWidth, self._imgHeight)
      
              # Proxy to ALVideoDevice.
              self._videoProxy = None
      
              # Our video module name.
              self._imgClient = ""
      
              # This will contain this alImage we get from Nao.
              self._alImage = None
      
              self._registerImageClient(IP, PORT)
      
              # Trigget 'timerEvent' every 100 ms.
              self.startTimer(100)
      
          def _registerImageClient(self, IP, PORT):
              """
              Register our video module to the robot.
              """
              self._videoProxy = ALProxy("ALVideoDevice", IP, PORT)
              resolution = vision_definitions.kQVGA  # 320 * 240
              colorSpace = vision_definitions.kRGBColorSpace
              self._imgClient = self._videoProxy.subscribe("_client", resolution, colorSpace, 5)
      
              # Select camera.
              self._videoProxy.setParam(vision_definitions.kCameraSelectID,
                                        self._cameraID)
      
          def _unregisterImageClient(self):
              """
              Unregister our naoqi video module.
              """
              if self._imgClient != "":
                  self._videoProxy.unsubscribe(self._imgClient)
      
          def paintEvent(self, event):
              """
              Draw the QImage on screen.
              """
              painter = QPainter(self)
              painter.drawImage(painter.viewport(), self._image)
      
          def _updateImage(self):
              """
              Retrieve a new image from Nao.
              """
              self._alImage = self._videoProxy.getImageRemote(self._imgClient)
              self._image = QImage(self._alImage[6],           # Pixel array.
                                   self._alImage[0],           # Width.
                                   self._alImage[1],           # Height.
                                   QImage.Format_RGB888)
      
          def timerEvent(self, event):
              """
              Called periodically. Retrieve a nao image, and update the widget.
              """
              self._updateImage()
              self.update()
      
          def __del__(self):
              """
              When the widget is deleted, we unregister our naoqi video module.
              """
              self._unregisterImageClient()
      
      if __name__ == '__main__':
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--camera", type=int, default=0,
                              help="Robot camera ID address. 0 by default")
      
          args = parser.parse_args()
          IP = args.pip
          PORT = args.pport
      
          CameraID = 0
      
          # Read IP address from first argument if any.
          if len(sys.argv) > 1:
              IP = sys.argv[1]
      
          # Read CameraID from second argument if any.
          if len(sys.argv) > 2:
              CameraID = int(sys.argv[2])
      
          app = QApplication(sys.argv)
          myWidget = ImageWidget(IP, PORT, CameraID)
          myWidget.show()
          sys.exit(app.exec_())
      ```
      </details>

  * **html/**
    * **blockly/**
      * **blockly/**
        * **.github/**
        * **accessible/**
          * **libs/**
          * **media/**
        * **appengine/**
          * `index_redirect.py`
            <details>
            <summary>View Content (Converted to Markdown)</summary>

            ```markdown
            print("Status: 302")
            print("Location: /static/demos/index.html")
            ```
            </details>

          * `storage.py`
            <details>
            <summary>View Content (Converted to Markdown)</summary>

            ```markdown
            """Blockly Demo: Storage
            
            Copyright 2012 Google Inc.
            https://developers.google.com/blockly/
            
            Licensed under the Apache License, Version 2.0 (the "License");
            you may not use this file except in compliance with the License.
            You may obtain a copy of the License at
            
              http://www.apache.org/licenses/LICENSE-2.0
            
            Unless required by applicable law or agreed to in writing, software
            distributed under the License is distributed on an "AS IS" BASIS,
            WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
            See the License for the specific language governing permissions and
            limitations under the License.
            """
            
            """Store and retrieve XML with App Engine.
            """
            
            __author__ = "q.neutron@gmail.com (Quynh Neutron)"
            
            import cgi
            from random import randint
            from google.appengine.ext import db
            from google.appengine.api import memcache
            import logging
            
            print "Content-Type: text/plain\n"
            
            def keyGen():
              # Generate a random string of length KEY_LEN.
              KEY_LEN = 6
              CHARS = "abcdefghijkmnopqrstuvwxyz23456789" # Exclude l, 0, 1.
              max_index = len(CHARS) - 1
              return "".join([CHARS[randint(0, max_index)] for x in range(KEY_LEN)])
            
            class Xml(db.Model):
              # A row in the database.
              xml_hash = db.IntegerProperty()
              xml_content = db.TextProperty()
            
            forms = cgi.FieldStorage()
            if "xml" in forms:
              # Store XML and return a generated key.
              xml_content = forms["xml"].value
              xml_hash = hash(xml_content)
              lookup_query = db.Query(Xml)
              lookup_query.filter("xml_hash =", xml_hash)
              lookup_result = lookup_query.get()
              if lookup_result:
                xml_key = lookup_result.key().name()
              else:
                trials = 0
                result = True
                while result:
                  trials += 1
                  if trials == 100:
                    raise Exception("Sorry, the generator failed to get a key for you.")
                  xml_key = keyGen()
                  result = db.get(db.Key.from_path("Xml", xml_key))
                xml = db.Text(xml_content, encoding="utf_8")
                row = Xml(key_name = xml_key, xml_hash = xml_hash, xml_content = xml)
                row.put()
              print xml_key
            
            if "key" in forms:
              # Retrieve stored XML based on the provided key.
              key_provided = forms["key"].value
              # Normalize the string.
              key_provided = key_provided.lower().strip()
              # Check memcache for a quick match.
              xml = memcache.get("XML_" + key_provided)
              if xml is None:
                # Check datastore for a definitive match.
                result = db.get(db.Key.from_path("Xml", key_provided))
                if not result:
                  xml = ""
                else:
                  xml = result.xml_content
                # Save to memcache for next hit.
                if not memcache.add("XML_" + key_provided, xml, 3600):
                  logging.error("Memcache set failed.")
              print xml.encode("utf-8")
            ```
            </details>

        * **blocks/**
        * `build.py`
          <details>
          <summary>View Content (Converted to Markdown)</summary>

          ```markdown
          #!/usr/bin/python2.7
          # Compresses the core Blockly files into a single JavaScript file.
          #
          # Copyright 2012 Google Inc.
          # https://developers.google.com/blockly/
          #
          # Licensed under the Apache License, Version 2.0 (the "License");
          # you may not use this file except in compliance with the License.
          # You may obtain a copy of the License at
          #
          #   http://www.apache.org/licenses/LICENSE-2.0
          #
          # Unless required by applicable law or agreed to in writing, software
          # distributed under the License is distributed on an "AS IS" BASIS,
          # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          # See the License for the specific language governing permissions and
          # limitations under the License.
          
          # Usage: build.py <0 or more of accessible, core, generators, langfiles>
          # build.py with no parameters builds all files.
          # core builds blockly_compressed, blockly_uncompressed, and blocks_compressed.
          # accessible builds blockly_accessible_compressed,
          #  blockly_accessible_uncompressed, and blocks_compressed.
          # generators builds every <language>_compressed.js.
          # langfiles builds every msg/js/<LANG>.js file.
          
          # This script generates four versions of Blockly's core files. The first pair
          # are:
          #   blockly_compressed.js
          #   blockly_uncompressed.js
          # The compressed file is a concatenation of all of Blockly's core files which
          # have been run through Google's Closure Compiler.  This is done using the
          # online API (which takes a few seconds and requires an Internet connection).
          # The uncompressed file is a script that loads in each of Blockly's core files
          # one by one.  This takes much longer for a browser to load, but is useful
          # when debugging code since line numbers are meaningful and variables haven't
          # been renamed.  The uncompressed file also allows for a faster development
          # cycle since there is no need to rebuild or recompile, just reload.
          #
          # The second pair are:
          #  blockly_accessible_compressed.js
          #  blockly_accessible_uncompressed.js
          # These files are analogous to blockly_compressed and blockly_uncompressed,
          # but also include the visually-impaired module for Blockly.
          #
          # This script also generates:
          #   blocks_compressed.js: The compressed Blockly language blocks.
          #   javascript_compressed.js: The compressed JavaScript generator.
          #   python_compressed.js: The compressed Python generator.
          #   php_compressed.js: The compressed PHP generator.
          #   lua_compressed.js: The compressed Lua generator.
          #   dart_compressed.js: The compressed Dart generator.
          #   msg/js/<LANG>.js for every language <LANG> defined in msg/js/<LANG>.json.
          
          import sys
          if sys.version_info[0] != 2:
            raise Exception("Blockly build only compatible with Python 2.x.\n"
                            "You are using: " + sys.version)
          
          for arg in sys.argv[1:len(sys.argv)]:
            if (arg != 'core' and
                arg != 'accessible' and
                arg != 'generators' and
                arg != 'langfiles'):
              raise Exception("Invalid argument: \"" + arg + "\". Usage: build.py "
                  "<0 or more of accessible, core, generators, langfiles>")
          
          import errno, glob, httplib, json, os, re, subprocess, threading, urllib
          
          def import_path(fullpath):
            """Import a file with full path specification.
            Allows one to import from any directory, something __import__ does not do.
          
            Args:
                fullpath:  Path and filename of import.
          
            Returns:
                An imported module.
            """
            path, filename = os.path.split(fullpath)
            filename, ext = os.path.splitext(filename)
            sys.path.append(path)
            module = __import__(filename)
            reload(module)  # Might be out of date.
            del sys.path[-1]
            return module
          
          HEADER = ("// Do not edit this file; automatically generated by build.py.\n"
                    "'use strict';\n")
          
          class Gen_uncompressed(threading.Thread):
            """Generate a JavaScript file that loads Blockly's raw files.
            Runs in a separate thread.
            """
            def __init__(self, search_paths, target_filename):
              threading.Thread.__init__(self)
              self.search_paths = search_paths
              self.target_filename = target_filename
          
            def run(self):
              f = open(self.target_filename, 'w')
              f.write(HEADER)
              f.write("""
          var isNodeJS = !!(typeof module !== 'undefined' && module.exports &&
                            typeof window === 'undefined');
          
          if (isNodeJS) {
            var window = {};
            require('closure-library');
          }
          
          window.BLOCKLY_DIR = (function() {
            if (!isNodeJS) {
              // Find name of current directory.
              var scripts = document.getElementsByTagName('script');
              var re = new RegExp('(.+)[\/]blockly_(.*)uncompressed\.js$');
              for (var i = 0, script; script = scripts[i]; i++) {
                var match = re.exec(script.src);
                if (match) {
                  return match[1];
                }
              }
              alert('Could not detect Blockly\\'s directory name.');
            }
            return '';
          })();
          
          window.BLOCKLY_BOOT = function() {
            var dir = '';
            if (isNodeJS) {
              require('closure-library');
              dir = 'blockly';
            } else {
              // Execute after Closure has loaded.
              if (!window.goog) {
                alert('Error: Closure not found.  Read this:\\n' +
                      'developers.google.com/blockly/guides/modify/web/closure');
              }
              dir = window.BLOCKLY_DIR.match(/[^\\/]+$/)[0];
            }
          """)
              add_dependency = []
              base_path = calcdeps.FindClosureBasePath(self.search_paths)
              for dep in calcdeps.BuildDependenciesFromFiles(self.search_paths):
                add_dependency.append(calcdeps.GetDepsLine(dep, base_path))
              add_dependency.sort()  # Deterministic build.
              add_dependency = '\n'.join(add_dependency)
              # Find the Blockly directory name and replace it with a JS variable.
              # This allows blockly_uncompressed.js to be compiled on one computer and be
              # used on another, even if the directory name differs.
              m = re.search('[\\/]([^\\/]+)[\\/]core[\\/]blockly.js', add_dependency)
              add_dependency = re.sub('([\\/])' + re.escape(m.group(1)) +
                  '([\\/](core|accessible)[\\/])', '\\1" + dir + "\\2', add_dependency)
              f.write(add_dependency + '\n')
          
              provides = []
              for dep in calcdeps.BuildDependenciesFromFiles(self.search_paths):
                if not dep.filename.startswith(os.pardir + os.sep):  # '../'
                  provides.extend(dep.provides)
              provides.sort()  # Deterministic build.
              f.write('\n')
              f.write('// Load Blockly.\n')
              for provide in provides:
                f.write("goog.require('%s');\n" % provide)
          
              f.write("""
          delete this.BLOCKLY_DIR;
          delete this.BLOCKLY_BOOT;
          };
          
          if (isNodeJS) {
            window.BLOCKLY_BOOT();
            module.exports = Blockly;
          } else {
            // Delete any existing Closure (e.g. Soy's nogoog_shim).
            document.write('<script>var goog = undefined;</script>');
            // Load fresh Closure Library.
            document.write('<script src="' + window.BLOCKLY_DIR +
                '/../closure-library/closure/goog/base.js"></script>');
            document.write('<script>window.BLOCKLY_BOOT();</script>');
          }
          """)
              f.close()
              print("SUCCESS: " + self.target_filename)
          
          class Gen_compressed(threading.Thread):
            """Generate a JavaScript file that contains all of Blockly's core and all
            required parts of Closure, compiled together.
            Uses the Closure Compiler's online API.
            Runs in a separate thread.
            """
            def __init__(self, search_paths, bundles):
              threading.Thread.__init__(self)
              self.search_paths = search_paths
              self.bundles = bundles
          
            def run(self):
              if ('core' in self.bundles):
                self.gen_core()
          
              if ('accessible' in self.bundles):
                self.gen_accessible()
          
              if ('core' in self.bundles or 'accessible' in self.bundles):
                self.gen_blocks()
          
              if ('generators' in self.bundles):
                self.gen_generator("javascript")
                self.gen_generator("python")
                self.gen_generator("php")
                self.gen_generator("lua")
                self.gen_generator("dart")
          
            def gen_core(self):
              target_filename = "blockly_compressed.js"
              # Define the parameters for the POST request.
              params = [
                  ("compilation_level", "SIMPLE_OPTIMIZATIONS"),
                  ("use_closure_library", "true"),
                  ("output_format", "json"),
                  ("output_info", "compiled_code"),
                  ("output_info", "warnings"),
                  ("output_info", "errors"),
                  ("output_info", "statistics"),
                ]
          
              # Read in all the source files.
              filenames = calcdeps.CalculateDependencies(self.search_paths,
                  [os.path.join("core", "blockly.js")])
              filenames.sort()  # Deterministic build.
              for filename in filenames:
                # Filter out the Closure files (the compiler will add them).
                if filename.startswith(os.pardir + os.sep):  # '../'
                  continue
                f = open(filename)
                params.append(("js_code", "".join(f.readlines())))
                f.close()
          
              self.do_compile(params, target_filename, filenames, "")
          
            def gen_accessible(self):
              target_filename = "blockly_accessible_compressed.js"
              # Define the parameters for the POST request.
              params = [
                  ("compilation_level", "SIMPLE_OPTIMIZATIONS"),
                  ("use_closure_library", "true"),
                  ("language_out", "ES5"),
                  ("output_format", "json"),
                  ("output_info", "compiled_code"),
                  ("output_info", "warnings"),
                  ("output_info", "errors"),
                  ("output_info", "statistics"),
                ]
          
              # Read in all the source files.
              filenames = calcdeps.CalculateDependencies(self.search_paths,
                  [os.path.join("accessible", "app.component.js")])
              filenames.sort()  # Deterministic build.
              for filename in filenames:
                # Filter out the Closure files (the compiler will add them).
                if filename.startswith(os.pardir + os.sep):  # '../'
                  continue
                f = open(filename)
                params.append(("js_code", "".join(f.readlines())))
                f.close()
          
              self.do_compile(params, target_filename, filenames, "")
          
            def gen_accessible(self):
              target_filename = "blockly_accessible_compressed.js"
              # Define the parameters for the POST request.
              params = [
                  ("compilation_level", "SIMPLE_OPTIMIZATIONS"),
                  ("use_closure_library", "true"),
                  ("language_out", "ES5"),
                  ("output_format", "json"),
                  ("output_info", "compiled_code"),
                  ("output_info", "warnings"),
                  ("output_info", "errors"),
                  ("output_info", "statistics"),
                ]
          
              # Read in all the source files.
              filenames = calcdeps.CalculateDependencies(self.search_paths,
                  [os.path.join("accessible", "app.component.js")])
              for filename in filenames:
                # Filter out the Closure files (the compiler will add them).
                if filename.startswith(os.pardir + os.sep):  # '../'
                  continue
                f = open(filename)
                params.append(("js_code", "".join(f.readlines())))
                f.close()
          
              self.do_compile(params, target_filename, filenames, "")
          
            def gen_blocks(self):
              target_filename = "blocks_compressed.js"
              # Define the parameters for the POST request.
              params = [
                  ("compilation_level", "SIMPLE_OPTIMIZATIONS"),
                  ("output_format", "json"),
                  ("output_info", "compiled_code"),
                  ("output_info", "warnings"),
                  ("output_info", "errors"),
                  ("output_info", "statistics"),
                ]
          
              # Read in all the source files.
              # Add Blockly.Blocks to be compatible with the compiler.
              params.append(("js_code", "goog.provide('Blockly');goog.provide('Blockly.Blocks');"))
              filenames = glob.glob(os.path.join("blocks", "*.js"))
              filenames.sort()  # Deterministic build.
              for filename in filenames:
                f = open(filename)
                params.append(("js_code", "".join(f.readlines())))
                f.close()
          
              # Remove Blockly.Blocks to be compatible with Blockly.
              remove = "var Blockly={Blocks:{}};"
              self.do_compile(params, target_filename, filenames, remove)
          
            def gen_generator(self, language):
              target_filename = language + "_compressed.js"
              # Define the parameters for the POST request.
              params = [
                  ("compilation_level", "SIMPLE_OPTIMIZATIONS"),
                  ("output_format", "json"),
                  ("output_info", "compiled_code"),
                  ("output_info", "warnings"),
                  ("output_info", "errors"),
                  ("output_info", "statistics"),
                ]
          
              # Read in all the source files.
              # Add Blockly.Generator to be compatible with the compiler.
              params.append(("js_code", "goog.provide('Blockly.Generator');"))
              filenames = glob.glob(
                  os.path.join("generators", language, "*.js"))
              filenames.sort()  # Deterministic build.
              filenames.insert(0, os.path.join("generators", language + ".js"))
              for filename in filenames:
                f = open(filename)
                params.append(("js_code", "".join(f.readlines())))
                f.close()
              filenames.insert(0, "[goog.provide]")
          
              # Remove Blockly.Generator to be compatible with Blockly.
              remove = "var Blockly={Generator:{}};"
              self.do_compile(params, target_filename, filenames, remove)
          
            def do_compile(self, params, target_filename, filenames, remove):
              # Send the request to Google.
              headers = {"Content-type": "application/x-www-form-urlencoded"}
              conn = httplib.HTTPSConnection("closure-compiler.appspot.com")
              conn.request("POST", "/compile", urllib.urlencode(params), headers)
              response = conn.getresponse()
              json_str = response.read()
              conn.close()
          
              # Parse the JSON response.
              try:
                json_data = json.loads(json_str)
              except ValueError:
                print("ERROR: Could not parse JSON for %s.  Raw data:" % target_filename)
                print(json_str)
                return
          
              def file_lookup(name):
                if not name.startswith("Input_"):
                  return "???"
                n = int(name[6:]) - 1
                return filenames[n]
          
              if json_data.has_key("serverErrors"):
                errors = json_data["serverErrors"]
                for error in errors:
                  print("SERVER ERROR: %s" % target_filename)
                  print(error["error"])
              elif json_data.has_key("errors"):
                errors = json_data["errors"]
                for error in errors:
                  print("FATAL ERROR")
                  print(error["error"])
                  if error["file"]:
                    print("%s at line %d:" % (
                        file_lookup(error["file"]), error["lineno"]))
                    print(error["line"])
                    print((" " * error["charno"]) + "^")
                  sys.exit(1)
              else:
                if json_data.has_key("warnings"):
                  warnings = json_data["warnings"]
                  for warning in warnings:
                    print("WARNING")
                    print(warning["warning"])
                    if warning["file"]:
                      print("%s at line %d:" % (
                          file_lookup(warning["file"]), warning["lineno"]))
                      print(warning["line"])
                      print((" " * warning["charno"]) + "^")
                  print()
          
                if not json_data.has_key("compiledCode"):
                  print("FATAL ERROR: Compiler did not return compiledCode.")
                  sys.exit(1)
          
                code = HEADER + "\n" + json_data["compiledCode"]
                code = code.replace(remove, "")
          
                # Trim down Google's (and only Google's) Apache licences.
                # The Closure Compiler preserves these.
                LICENSE = re.compile("""/\\*
          
           [\w ]+
          
           Copyright \\d+ Google Inc.
           https://developers.google.com/blockly/
          
           Licensed under the Apache License, Version 2.0 \(the "License"\);
           you may not use this file except in compliance with the License.
           You may obtain a copy of the License at
          
             http://www.apache.org/licenses/LICENSE-2.0
          
           Unless required by applicable law or agreed to in writing, software
           distributed under the License is distributed on an "AS IS" BASIS,
           WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
           See the License for the specific language governing permissions and
           limitations under the License.
          \\*/""")
                code = re.sub(LICENSE, "", code)
          
                stats = json_data["statistics"]
                original_b = stats["originalSize"]
                compressed_b = stats["compressedSize"]
                if original_b > 0 and compressed_b > 0:
                  f = open(target_filename, "w")
                  f.write(code)
                  f.close()
          
                  original_kb = int(original_b / 1024 + 0.5)
                  compressed_kb = int(compressed_b / 1024 + 0.5)
                  ratio = int(float(compressed_b) / float(original_b) * 100 + 0.5)
                  print("SUCCESS: " + target_filename)
                  print("Size changed from %d KB to %d KB (%d%%)." % (
                      original_kb, compressed_kb, ratio))
                else:
                  print("UNKNOWN ERROR")
          
          class Gen_langfiles(threading.Thread):
            """Generate JavaScript file for each natural language supported.
          
            Runs in a separate thread.
            """
          
            def __init__(self, force_gen):
              threading.Thread.__init__(self)
              self.force_gen = force_gen
          
            def _rebuild(self, srcs, dests):
              # Determine whether any of the files in srcs is newer than any in dests.
              try:
                return (max(os.path.getmtime(src) for src in srcs) >
                        min(os.path.getmtime(dest) for dest in dests))
              except OSError as e:
                # Was a file not found?
                if e.errno == errno.ENOENT:
                  # If it was a source file, we can't proceed.
                  if e.filename in srcs:
                    print("Source file missing: " + e.filename)
                    sys.exit(1)
                  else:
                    # If a destination file was missing, rebuild.
                    return True
                else:
                  print("Error checking file creation times: " + e)
          
            def run(self):
              # The files msg/json/{en,qqq,synonyms}.json depend on msg/messages.js.
              if (self.force_gen or
                  self._rebuild([os.path.join("msg", "messages.js")],
                                [os.path.join("msg", "json", f) for f in
                                ["en.json", "qqq.json", "synonyms.json"]])):
                try:
                  subprocess.check_call([
                      "python",
                      os.path.join("i18n", "js_to_json.py"),
                      "--input_file", "msg/messages.js",
                      "--output_dir", "msg/json/",
                      "--quiet"])
                except (subprocess.CalledProcessError, OSError) as e:
                  # Documentation for subprocess.check_call says that CalledProcessError
                  # will be raised on failure, but I found that OSError is also possible.
                  print("Error running i18n/js_to_json.py: ", e)
                  sys.exit(1)
          
              # Checking whether it is necessary to rebuild the js files would be a lot of
              # work since we would have to compare each <lang>.json file with each
              # <lang>.js file.  Rebuilding is easy and cheap, so just go ahead and do it.
              try:
                # Use create_messages.py to create .js files from .json files.
                cmd = [
                    "python",
                    os.path.join("i18n", "create_messages.py"),
                    "--source_lang_file", os.path.join("msg", "json", "en.json"),
                    "--source_synonym_file", os.path.join("msg", "json", "synonyms.json"),
                    "--source_constants_file", os.path.join("msg", "json", "constants.json"),
                    "--key_file", os.path.join("msg", "json", "keys.json"),
                    "--output_dir", os.path.join("msg", "js"),
                    "--quiet"]
                json_files = glob.glob(os.path.join("msg", "json", "*.json"))
                json_files = [file for file in json_files if not
                              (file.endswith(("keys.json", "synonyms.json", "qqq.json", "constants.json")))]
                cmd.extend(json_files)
                subprocess.check_call(cmd)
              except (subprocess.CalledProcessError, OSError) as e:
                print("Error running i18n/create_messages.py: ", e)
                sys.exit(1)
          
              # Output list of .js files created.
              for f in json_files:
                # This assumes the path to the current directory does not contain "json".
                f = f.replace("json", "js")
                if os.path.isfile(f):
                  print("SUCCESS: " + f)
                else:
                  print("FAILED to create " + f)
          
          if __name__ == "__main__":
            try:
              calcdeps = import_path(os.path.join(
                  os.path.pardir, "closure-library", "closure", "bin", "calcdeps.py"))
            except ImportError:
              if os.path.isdir(os.path.join(os.path.pardir, "closure-library-read-only")):
                # Dir got renamed when Closure moved from Google Code to GitHub in 2014.
                print("Error: Closure directory needs to be renamed from"
                      "'closure-library-read-only' to 'closure-library'.\n"
                      "Please rename this directory.")
              elif os.path.isdir(os.path.join(os.path.pardir, "google-closure-library")):
                # When Closure is installed by npm, it is named "google-closure-library".
                #calcdeps = import_path(os.path.join(
                # os.path.pardir, "google-closure-library", "closure", "bin", "calcdeps.py"))
                print("Error: Closure directory needs to be renamed from"
                     "'google-closure-library' to 'closure-library'.\n"
                     "Please rename this directory.")
              else:
                print("""Error: Closure not found.  Read this:
          developers.google.com/blockly/guides/modify/web/closure""")
              sys.exit(1)
          
            core_search_paths = calcdeps.ExpandDirectories(
                ["core", os.path.join(os.path.pardir, "closure-library")])
            core_search_paths.sort()  # Deterministic build.
            full_search_paths = calcdeps.ExpandDirectories(
                ["accessible", "core", os.path.join(os.path.pardir, "closure-library")])
            full_search_paths.sort()  # Deterministic build.
          
            if (len(sys.argv) == 1):
              args = ['core', 'accessible', 'generators', 'defaultlangfiles']
            else:
              args = sys.argv
          
            # Uncompressed and compressed are run in parallel threads.
            # Uncompressed is limited by processor speed.
            if ('core' in args):
              Gen_uncompressed(core_search_paths, 'blockly_uncompressed.js').start()
          
            if ('accessible' in args):
              Gen_uncompressed(full_search_paths, 'blockly_accessible_uncompressed.js').start()
          
            # Compressed is limited by network and server speed.
            Gen_compressed(full_search_paths, args).start()
          
            # This is run locally in a separate thread
            # defaultlangfiles checks for changes in the msg files, while manually asking
            # to build langfiles will force the messages to be rebuilt.
            if ('langfiles' in args or 'defaultlangfiles' in args):
              Gen_langfiles('langfiles' in args).start()
          ```
          </details>

        * **core/**
        * **demos/**
          * **accessible/**
          * **blockfactory/**
            * **workspacefactory/**
          * **blockfactory_old/**
          * **code/**
            * **msg/**
          * **codelab/**
            * **app/**
              * **scripts/**
              * **sounds/**
              * **styles/**
            * **app-complete/**
              * **scripts/**
              * **sounds/**
              * **styles/**
          * **custom-dialogs/**
          * **fixed/**
          * **generator/**
          * **graph/**
          * **headless/**
          * **interpreter/**
          * **maxBlocks/**
          * **minimap/**
          * **mirror/**
          * **plane/**
            * **generated/**
            * **soy/**
            * **xlf/**
          * **resizable/**
          * **rtl/**
          * **storage/**
          * **toolbox/**
        * **externs/**
        * **generators/**
          * **dart/**
          * **javascript/**
          * **lua/**
          * **php/**
          * **python/**
        * **i18n/**
          * `common.py`
            <details>
            <summary>View Content (Converted to Markdown)</summary>

            ```markdown
            #!/usr/bin/python
            
            # Code shared by translation conversion scripts.
            #
            # Copyright 2013 Google Inc.
            # https://developers.google.com/blockly/
            #
            # Licensed under the Apache License, Version 2.0 (the "License");
            # you may not use this file except in compliance with the License.
            # You may obtain a copy of the License at
            #
            #   http://www.apache.org/licenses/LICENSE-2.0
            #
            # Unless required by applicable law or agreed to in writing, software
            # distributed under the License is distributed on an "AS IS" BASIS,
            # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
            # See the License for the specific language governing permissions and
            # limitations under the License.
            
            import codecs
            import json
            import os
            from datetime import datetime
            
            class InputError(Exception):
                """Exception raised for errors in the input.
            
                Attributes:
                    location -- where error occurred
                    msg -- explanation of the error
            
                """
            
                def __init__(self, location, msg):
                    Exception.__init__(self, '{0}: {1}'.format(location, msg))
                    self.location = location
                    self.msg = msg
            
            def read_json_file(filename):
              """Read a JSON file as UTF-8 into a dictionary, discarding @metadata.
            
              Args:
                filename: The filename, which must end ".json".
            
              Returns:
                The dictionary.
            
              Raises:
                InputError: The filename did not end with ".json" or an error occurred
                    while opening or reading the file.
              """
              if not filename.endswith('.json'):
                raise InputError(filename, 'filenames must end with ".json"')
              try:
                # Read in file.
                with codecs.open(filename, 'r', 'utf-8') as infile:
                  defs = json.load(infile)
                if '@metadata' in defs:
                  del defs['@metadata']
                return defs
              except ValueError, e:
                print('Error reading ' + filename)
                raise InputError(filename, str(e))
            
            def _create_qqq_file(output_dir):
                """Creates a qqq.json file with message documentation for translatewiki.net.
            
                The file consists of key-value pairs, where the keys are message ids and
                the values are descriptions for the translators of the messages.
                What documentation exists for the format can be found at:
                http://translatewiki.net/wiki/Translating:Localisation_for_developers#Message_documentation
            
                The file should be closed by _close_qqq_file().
            
                Parameters:
                    output_dir: The output directory.
            
                Returns:
                    A pointer to a file to which a left brace and newline have been written.
            
                Raises:
                    IOError: An error occurred while opening or writing the file.
                """
                qqq_file_name = os.path.join(os.curdir, output_dir, 'qqq.json')
                qqq_file = codecs.open(qqq_file_name, 'w', 'utf-8')
                print 'Created file: ' + qqq_file_name
                qqq_file.write('{\n')
                return qqq_file
            
            def _close_qqq_file(qqq_file):
                """Closes a qqq.json file created and opened by _create_qqq_file().
            
                This writes the final newlines and right brace.
            
                Args:
                    qqq_file: A file created by _create_qqq_file().
            
                Raises:
                    IOError: An error occurred while writing to or closing the file.
                """
                qqq_file.write('\n}\n')
                qqq_file.close()
            
            def _create_lang_file(author, lang, output_dir):
                """Creates a <lang>.json file for translatewiki.net.
            
                The file consists of metadata, followed by key-value pairs, where the keys
                are message ids and the values are the messages in the language specified
                by the corresponding command-line argument.  The file should be closed by
                _close_lang_file().
            
                Args:
                    author: Name and email address of contact for translators.
                    lang: ISO 639-1 source language code.
                    output_dir: Relative directory for output files.
            
                Returns:
                    A pointer to a file to which the metadata has been written.
            
                Raises:
                    IOError: An error occurred while opening or writing the file.
                """
                lang_file_name = os.path.join(os.curdir, output_dir, lang + '.json')
                lang_file = codecs.open(lang_file_name, 'w', 'utf-8')
                print 'Created file: ' + lang_file_name
                # string.format doesn't like printing braces, so break up our writes.
                lang_file.write('{\n\t"@metadata": {')
                lang_file.write("""
            \t\t"author": "{0}",
            \t\t"lastupdated": "{1}",
            \t\t"locale": "{2}",
            \t\t"messagedocumentation" : "qqq"
            """.format(author, str(datetime.now()), lang))
                lang_file.write('\t},\n')
                return lang_file
            
            def _close_lang_file(lang_file):
                """Closes a <lang>.json file created with _create_lang_file().
            
                This also writes the terminating left brace and newline.
            
                Args:
                    lang_file: A file opened with _create_lang_file().
            
                Raises:
                    IOError: An error occurred while writing to or closing the file.
                """
                lang_file.write('\n}\n')
                lang_file.close()
            
            def _create_key_file(output_dir):
                """Creates a keys.json file mapping Closure keys to Blockly keys.
            
                Args:
                    output_dir: Relative directory for output files.
            
                Raises:
                    IOError: An error occurred while creating the file.
                """
                key_file_name = os.path.join(os.curdir, output_dir, 'keys.json')
                key_file = open(key_file_name, 'w')
                key_file.write('{\n')
                print 'Created file: ' + key_file_name
                return key_file
            
            def _close_key_file(key_file):
                """Closes a key file created and opened with _create_key_file().
            
                Args:
                    key_file: A file created by _create_key_file().
            
                Raises:
                    IOError: An error occurred while writing to or closing the file.
                """
                key_file.write('\n}\n')
                key_file.close()
            
            def write_files(author, lang, output_dir, units, write_key_file):
                """Writes the output files for the given units.
            
                There are three possible output files:
                * lang_file: JSON file mapping meanings (e.g., Maze.turnLeft) to the
                  English text.  The base name of the language file is specified by the
                  "lang" command-line argument.
                * key_file: JSON file mapping meanings to Soy-generated keys (long hash
                  codes).  This is only output if the parameter write_key_file is True.
                * qqq_file: JSON file mapping meanings to descriptions.
            
                Args:
                    author: Name and email address of contact for translators.
                    lang: ISO 639-1 source language code.
                    output_dir: Relative directory for output files.
                    units: A list of dictionaries with entries for 'meaning', 'source',
                        'description', and 'keys' (the last only if write_key_file is true),
                        in the order desired in the output files.
                    write_key_file: Whether to output a keys.json file.
            
                Raises:
                    IOError: An error occurs opening, writing to, or closing a file.
                    KeyError: An expected key is missing from units.
                """
                lang_file = _create_lang_file(author, lang, output_dir)
                qqq_file = _create_qqq_file(output_dir)
                if write_key_file:
                  key_file = _create_key_file(output_dir)
                first_entry = True
                for unit in units:
                    if not first_entry:
                        lang_file.write(',\n')
                        if write_key_file:
                          key_file.write(',\n')
                        qqq_file.write(',\n')
                    lang_file.write(u'\t"{0}": "{1}"'.format(
                        unit['meaning'],
                        unit['source'].replace('"', "'")))
                    if write_key_file:
                      key_file.write('"{0}": "{1}"'.format(unit['meaning'], unit['key']))
                    qqq_file.write(u'\t"{0}": "{1}"'.format(
                        unit['meaning'],
                        unit['description'].replace('"', "'").replace(
                            '{lb}', '{').replace('{rb}', '}')))
                    first_entry = False
                _close_lang_file(lang_file)
                if write_key_file:
                  _close_key_file(key_file)
                _close_qqq_file(qqq_file)
            ```
            </details>

          * `create_messages.py`
            <details>
            <summary>View Content (Converted to Markdown)</summary>

            ```markdown
            #!/usr/bin/python
            
            # Generate .js files defining Blockly core and language messages.
            #
            # Copyright 2013 Google Inc.
            # https://developers.google.com/blockly/
            #
            # Licensed under the Apache License, Version 2.0 (the "License");
            # you may not use this file except in compliance with the License.
            # You may obtain a copy of the License at
            #
            #   http://www.apache.org/licenses/LICENSE-2.0
            #
            # Unless required by applicable law or agreed to in writing, software
            # distributed under the License is distributed on an "AS IS" BASIS,
            # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
            # See the License for the specific language governing permissions and
            # limitations under the License.
            
            import argparse
            import codecs
            import os
            import re
            import sys
            from common import read_json_file
            
            _NEWLINE_PATTERN = re.compile('[\n\r]')
            
            def string_is_ascii(s):
              try:
                s.decode('ascii')
                return True
              except UnicodeEncodeError:
                return False
            
            def load_constants(filename):
              """Read in constants file, which must be output in every language."""
              constant_defs = read_json_file(filename);
              constants_text = '\n'
              for key in constant_defs:
                value = constant_defs[key]
                value = value.replace('"', '\\"')
                constants_text += u'\n/** @export */ Blockly.Msg.{0} = \"{1}\";'.format(
                    key, value)
              return constants_text
            
            def main():
              """Generate .js files defining Blockly core and language messages."""
            
              # Process command-line arguments.
              parser = argparse.ArgumentParser(description='Convert JSON files to JS.')
              parser.add_argument('--source_lang', default='en',
                                  help='ISO 639-1 source language code')
              parser.add_argument('--source_lang_file',
                                  default=os.path.join('json', 'en.json'),
                                  help='Path to .json file for source language')
              parser.add_argument('--source_synonym_file',
                                  default=os.path.join('json', 'synonyms.json'),
                                  help='Path to .json file with synonym definitions')
              parser.add_argument('--source_constants_file',
                                  default=os.path.join('json', 'constants.json'),
                                  help='Path to .json file with constant definitions')
              parser.add_argument('--output_dir', default='js/',
                                  help='relative directory for output files')
              parser.add_argument('--key_file', default='keys.json',
                                  help='relative path to input keys file')
              parser.add_argument('--quiet', action='store_true', default=False,
                                  help='do not write anything to standard output')
              parser.add_argument('files', nargs='+', help='input files')
              args = parser.parse_args()
              if not args.output_dir.endswith(os.path.sep):
                args.output_dir += os.path.sep
            
              # Read in source language .json file, which provides any values missing
              # in target languages' .json files.
              source_defs = read_json_file(os.path.join(os.curdir, args.source_lang_file))
              # Make sure the source file doesn't contain a newline or carriage return.
              for key, value in source_defs.items():
                if _NEWLINE_PATTERN.search(value):
                  print('ERROR: definition of {0} in {1} contained a newline character.'.
                        format(key, args.source_lang_file))
                  sys.exit(1)
              sorted_keys = source_defs.keys()
              sorted_keys.sort()
            
              # Read in synonyms file, which must be output in every language.
              synonym_defs = read_json_file(os.path.join(
                  os.curdir, args.source_synonym_file))
              synonym_text = '\n'.join([u'/** @export */ Blockly.Msg.{0} = Blockly.Msg.{1};'
                  .format(key, synonym_defs[key]) for key in synonym_defs])
            
              # Read in constants file, which must be output in every language.
              constants_text = load_constants(os.path.join(os.curdir, args.source_constants_file))
            
              # Create each output file.
              for arg_file in args.files:
                (_, filename) = os.path.split(arg_file)
                target_lang = filename[:filename.index('.')]
                if target_lang not in ('qqq', 'keys', 'synonyms', 'constants'):
                  target_defs = read_json_file(os.path.join(os.curdir, arg_file))
            
                  # Verify that keys are 'ascii'
                  bad_keys = [key for key in target_defs if not string_is_ascii(key)]
                  if bad_keys:
                    print(u'These keys in {0} contain non ascii characters: {1}'.format(
                        filename, ', '.join(bad_keys)))
            
                  # If there's a '\n' or '\r', remove it and print a warning.
                  for key, value in target_defs.items():
                    if _NEWLINE_PATTERN.search(value):
                      print(u'WARNING: definition of {0} in {1} contained '
                            'a newline character.'.
                            format(key, arg_file))
                      target_defs[key] = _NEWLINE_PATTERN.sub(' ', value)
            
                  # Output file.
                  outname = os.path.join(os.curdir, args.output_dir, target_lang + '.js')
                  with codecs.open(outname, 'w', 'utf-8') as outfile:
                    outfile.write(
                        """// This file was automatically generated.  Do not modify.
            
            'use strict';
            
            goog.provide('Blockly.Msg.{0}');
            
            goog.require('Blockly.Msg');
            
            """.format(target_lang.replace('-', '.')))
                    # For each key in the source language file, output the target value
                    # if present; otherwise, output the source language value with a
                    # warning comment.
                    for key in sorted_keys:
                      if key in target_defs:
                        value = target_defs[key]
                        comment = ''
                        del target_defs[key]
                      else:
                        value = source_defs[key]
                        comment = '  // untranslated'
                      value = value.replace('"', '\\"')
                      outfile.write(u'/** @export */ Blockly.Msg.{0} = "{1}";{2}\n'
                          .format(key, value, comment))
            
                    # Announce any keys defined only for target language.
                    if target_defs:
                      extra_keys = [key for key in target_defs if key not in synonym_defs]
                      synonym_keys = [key for key in target_defs if key in synonym_defs]
                      if not args.quiet:
                        if extra_keys:
                          print(u'These extra keys appeared in {0}: {1}'.format(
                              filename, ', '.join(extra_keys)))
                        if synonym_keys:
                          print(u'These synonym keys appeared in {0}: {1}'.format(
                              filename, ', '.join(synonym_keys)))
            
                    outfile.write(synonym_text)
                    outfile.write(constants_text)
            
                  if not args.quiet:
                    print('Created {0}.'.format(outname))
            
            if __name__ == '__main__':
              main()
            ```
            </details>

          * `dedup_json.py`
            <details>
            <summary>View Content (Converted to Markdown)</summary>

            ```markdown
            #!/usr/bin/python
            
            # Consolidates duplicate key-value pairs in a JSON file.
            # If the same key is used with different values, no warning is given,
            # and there is no guarantee about which key-value pair will be output.
            # There is also no guarantee as to the order of the key-value pairs
            # output.
            #
            # Copyright 2013 Google Inc.
            # https://developers.google.com/blockly/
            #
            # Licensed under the Apache License, Version 2.0 (the "License");
            # you may not use this file except in compliance with the License.
            # You may obtain a copy of the License at
            #
            #   http://www.apache.org/licenses/LICENSE-2.0
            #
            # Unless required by applicable law or agreed to in writing, software
            # distributed under the License is distributed on an "AS IS" BASIS,
            # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
            # See the License for the specific language governing permissions and
            # limitations under the License.
            
            import argparse
            import codecs
            import json
            from common import InputError
            
            def main():
              """Parses arguments and iterates over files.
            
              Raises:
                IOError: An I/O error occurred with an input or output file.
                InputError: Input JSON could not be parsed.
              """
            
              # Set up argument parser.
              parser = argparse.ArgumentParser(
                  description='Removes duplicate key-value pairs from JSON files.')
              parser.add_argument('--suffix', default='',
                                  help='optional suffix for output files; '
                                  'if empty, files will be changed in place')
              parser.add_argument('files', nargs='+', help='input files')
              args = parser.parse_args()
            
              # Iterate over files.
              for filename in args.files:
                # Read in json using Python libraries.  This eliminates duplicates.
                print('Processing ' + filename + '...')
                try:
                  with codecs.open(filename, 'r', 'utf-8') as infile:
                    j = json.load(infile)
                except ValueError, e:
                  print('Error reading ' + filename)
                  raise InputError(file, str(e))
            
                # Built up output strings as an array to make output of delimiters easier.
                output = []
                for key in j:
                  if key != '@metadata':
                    output.append('\t"' + key + '": "' +
                                  j[key].replace('\n', '\\n') + '"')
            
                # Output results.
                with codecs.open(filename + args.suffix, 'w', 'utf-8') as outfile:
                  outfile.write('{\n')
                  outfile.write(',\n'.join(output))
                  outfile.write('\n}\n')
            
            if __name__ == '__main__':
              main()
            ```
            </details>

          * `js_to_json.py`
            <details>
            <summary>View Content (Converted to Markdown)</summary>

            ```markdown
            #!/usr/bin/python
            
            # Gives the translation status of the specified apps and languages.
            #
            # Copyright 2013 Google Inc.
            # https://developers.google.com/blockly/
            #
            # Licensed under the Apache License, Version 2.0 (the "License");
            # you may not use this file except in compliance with the License.
            # You may obtain a copy of the License at
            #
            #   http://www.apache.org/licenses/LICENSE-2.0
            #
            # Unless required by applicable law or agreed to in writing, software
            # distributed under the License is distributed on an "AS IS" BASIS,
            # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
            # See the License for the specific language governing permissions and
            # limitations under the License.
            
            """Extracts messages from .js files into .json files for translation.
            
            Specifically, lines with the following formats are extracted:
            
                /// Here is a description of the following message.
                Blockly.SOME_KEY = 'Some value';
            
            Adjacent "///" lines are concatenated.
            
            There are two output files, each of which is proper JSON.  For each key, the
            file en.json would get an entry of the form:
            
                "Blockly.SOME_KEY", "Some value",
            
            The file qqq.json would get:
            
                "Blockly.SOME_KEY", "Here is a description of the following message.",
            
            Commas would of course be omitted for the final entry of each value.
            
            @author Ellen Spertus (ellen.spertus@gmail.com)
            """
            
            import argparse
            import codecs
            import json
            import os
            import re
            from common import write_files
            
            _INPUT_DEF_PATTERN = re.compile("""Blockly.Msg.(\w*)\s*=\s*'(.*)';?\r?$""")
            
            _INPUT_SYN_PATTERN = re.compile(
                """Blockly.Msg.(\w*)\s*=\s*Blockly.Msg.(\w*);""")
            
            _CONSTANT_DESCRIPTION_PATTERN = re.compile(
                """{{Notranslate}}""", re.IGNORECASE)
            
            def main():
              # Set up argument parser.
              parser = argparse.ArgumentParser(description='Create translation files.')
              parser.add_argument(
                  '--author',
                  default='Ellen Spertus <ellen.spertus@gmail.com>',
                  help='name and email address of contact for translators')
              parser.add_argument('--lang', default='en',
                                  help='ISO 639-1 source language code')
              parser.add_argument('--output_dir', default='json',
                                  help='relative directory for output files')
              parser.add_argument('--input_file', default='messages.js',
                                  help='input file')
              parser.add_argument('--quiet', action='store_true', default=False,
                                  help='only display warnings, not routine info')
              args = parser.parse_args()
              if (not args.output_dir.endswith(os.path.sep)):
                args.output_dir += os.path.sep
            
              # Read and parse input file.
              results = []
              synonyms = {}
              constants = {}  # Values that are constant across all languages.
              description = ''
              infile = codecs.open(args.input_file, 'r', 'utf-8')
              for line in infile:
                if line.startswith('///'):
                  if description:
                    description = description + ' ' + line[3:].strip()
                  else:
                    description = line[3:].strip()
                else:
                  match = _INPUT_DEF_PATTERN.match(line)
                  if match:
                    key = match.group(1)
                    value = match.group(2).replace("\\'", "'")
                    if not description:
                      print('Warning: No description for ' + result['meaning'])
                    if (description and _CONSTANT_DESCRIPTION_PATTERN.search(description)):
                      constants[key] = value
                    else:
                      result = {}
                      result['meaning'] = key
                      result['source'] = value
                      result['description'] = description
                      results.append(result)
                    description = ''
                  else:
                    match = _INPUT_SYN_PATTERN.match(line)
                    if match:
                      if description:
                        print('Warning: Description preceding definition of synonym {0}.'.
                              format(match.group(1)))
                        description = ''
                      synonyms[match.group(1)] = match.group(2)
              infile.close()
            
              # Create <lang_file>.json, keys.json, and qqq.json.
              write_files(args.author, args.lang, args.output_dir, results, False)
            
              # Create synonyms.json.
              synonym_file_name = os.path.join(os.curdir, args.output_dir, 'synonyms.json')
              with open(synonym_file_name, 'w') as outfile:
                json.dump(synonyms, outfile)
              if not args.quiet:
                print("Wrote {0} synonym pairs to {1}.".format(
                    len(synonyms), synonym_file_name))
            
              # Create constants.json
              constants_file_name = os.path.join(os.curdir, args.output_dir, 'constants.json')
              with open(constants_file_name, 'w') as outfile:
                json.dump(constants, outfile)
              if not args.quiet:
                print("Wrote {0} constant pairs to {1}.".format(
                    len(constants), synonym_file_name))
            
            if __name__ == '__main__':
              main()
            ```
            </details>

          * `json_to_js.py`
            <details>
            <summary>View Content (Converted to Markdown)</summary>

            ```markdown
            #!/usr/bin/python
            
            # Converts .json files into .js files for use within Blockly apps.
            #
            # Copyright 2013 Google Inc.
            # https://developers.google.com/blockly/
            #
            # Licensed under the Apache License, Version 2.0 (the "License");
            # you may not use this file except in compliance with the License.
            # You may obtain a copy of the License at
            #
            #   http://www.apache.org/licenses/LICENSE-2.0
            #
            # Unless required by applicable law or agreed to in writing, software
            # distributed under the License is distributed on an "AS IS" BASIS,
            # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
            # See the License for the specific language governing permissions and
            # limitations under the License.
            
            import argparse
            import codecs      # for codecs.open(..., 'utf-8')
            import glob
            import json        # for json.load()
            import os          # for os.path()
            import subprocess  # for subprocess.check_call()
            from common import InputError
            from common import read_json_file
            
            # Store parsed command-line arguments in global variable.
            args = None
            
            def _create_xlf(target_lang):
                """Creates a <target_lang>.xlf file for Soy.
            
                Args:
                    target_lang: The ISO 639 language code for the target language.
                        This is used in the name of the file and in the metadata.
            
                Returns:
                    A pointer to a file to which the metadata has been written.
            
                Raises:
                    IOError: An error occurred while opening or writing the file.
                """
                filename = os.path.join(os.curdir, args.output_dir, target_lang + '.xlf')
                out_file = codecs.open(filename, 'w', 'utf-8')
                out_file.write("""<?xml version="1.0" encoding="UTF-8"?>
            <xliff version="1.2" xmlns="urn:oasis:names:tc:xliff:document:1.2">
              <file original="SoyMsgBundle"
                    datatype="x-soy-msg-bundle"
                    xml:space="preserve"
                    source-language="{0}"
                    target-language="{1}">
                <body>""".format(args.source_lang, target_lang))
                return out_file
            
            def _close_xlf(xlf_file):
                """Closes a <target_lang>.xlf file created with create_xlf().
            
                This includes writing the terminating XML.
            
                Args:
                    xlf_file: A pointer to a file created by _create_xlf().
            
                Raises:
                    IOError: An error occurred while writing to or closing the file.
                """
                xlf_file.write("""
                </body>
              </file>
            </xliff>
            """)
                xlf_file.close()
            
            def _process_file(path_to_json, target_lang, key_dict):
                """Creates an .xlf file corresponding to the specified .json input file.
            
                The name of the input file must be target_lang followed by '.json'.
                The name of the output file will be target_lang followed by '.js'.
            
                Args:
                    path_to_json: Path to the directory of xx.json files.
                    target_lang: A IETF language code (RFC 4646), such as 'es' or 'pt-br'.
                    key_dict: Dictionary mapping Blockly keys (e.g., Maze.turnLeft) to
                        Closure keys (hash numbers).
            
                Raises:
                    IOError: An I/O error occurred with an input or output file.
                    InputError: Input JSON could not be parsed.
                    KeyError: Key found in input file but not in key file.
                """
                keyfile = os.path.join(path_to_json, target_lang + '.json')
                j = read_json_file(keyfile)
                out_file = _create_xlf(target_lang)
                for key in j:
                    if key != '@metadata':
                        try:
                            identifier = key_dict[key]
                        except KeyError, e:
                            print('Key "%s" is in %s but not in %s' %
                                  (key, keyfile, args.key_file))
                            raise e
                        target = j.get(key)
                        out_file.write(u"""
                  <trans-unit id="{0}" datatype="html">
                    <target>{1}</target>
                  </trans-unit>""".format(identifier, target))
                _close_xlf(out_file)
            
            def main():
                """Parses arguments and iterates over files."""
            
                # Set up argument parser.
                parser = argparse.ArgumentParser(description='Convert JSON files to JS.')
                parser.add_argument('--source_lang', default='en',
                                    help='ISO 639-1 source language code')
                parser.add_argument('--output_dir', default='generated',
                                    help='relative directory for output files')
                parser.add_argument('--key_file', default='json' + os.path.sep + 'keys.json',
                                    help='relative path to input keys file')
                parser.add_argument('--template', default='template.soy')
                parser.add_argument('--path_to_jar',
                                    default='..' + os.path.sep + 'apps' + os.path.sep
                                    + '_soy',
                                    help='relative path from working directory to '
                                    'SoyToJsSrcCompiler.jar')
                parser.add_argument('files', nargs='+', help='input files')
            
                # Initialize global variables.
                global args
                args = parser.parse_args()
            
                # Make sure output_dir ends with slash.
                if (not args.output_dir.endswith(os.path.sep)):
                  args.output_dir += os.path.sep
            
                # Read in keys.json, mapping descriptions (e.g., Maze.turnLeft) to
                # Closure keys (long hash numbers).
                key_file = open(args.key_file)
                key_dict = json.load(key_file)
                key_file.close()
            
                # Process each input file.
                print('Creating .xlf files...')
                processed_langs = []
                if len(args.files) == 1:
                  # Windows does not expand globs automatically.
                  args.files = glob.glob(args.files[0])
                for arg_file in args.files:
                  (path_to_json, filename) = os.path.split(arg_file)
                  if not filename.endswith('.json'):
                    raise InputError(filename, 'filenames must end with ".json"')
                  target_lang = filename[:filename.index('.')]
                  if not target_lang in ('qqq', 'keys'):
                    processed_langs.append(target_lang)
                    _process_file(path_to_json, target_lang, key_dict)
            
                # Output command line for Closure compiler.
                if processed_langs:
                  print('Creating .js files...')
                  processed_lang_list = ','.join(processed_langs)
                  subprocess.check_call([
                      'java',
                      '-jar', os.path.join(args.path_to_jar, 'SoyToJsSrcCompiler.jar'),
                      '--locales', processed_lang_list,
                      '--messageFilePathFormat', args.output_dir + '{LOCALE}.xlf',
                      '--outputPathFormat', args.output_dir + '{LOCALE}.js',
                      '--srcs', args.template])
                  if len(processed_langs) == 1:
                    print('Created ' + processed_lang_list + '.js in ' + args.output_dir)
                  else:
                    print('Created {' + processed_lang_list + '}.js in ' + args.output_dir)
            
                  for lang in processed_langs:
                    os.remove(args.output_dir + lang + '.xlf')
                  print('Removed .xlf files.')
            
            if __name__ == '__main__':
                main()
            ```
            </details>

          * `tests.py`
            <details>
            <summary>View Content (Converted to Markdown)</summary>

            ```markdown
            #!/usr/bin/python
            # -*- coding: utf-8 -*-
            
            # Tests of i18n scripts.
            #
            # Copyright 2013 Google Inc.
            # https://developers.google.com/blockly/
            #
            # Licensed under the Apache License, Version 2.0 (the "License");
            # you may not use this file except in compliance with the License.
            # You may obtain a copy of the License at
            #
            #   http://www.apache.org/licenses/LICENSE-2.0
            #
            # Unless required by applicable law or agreed to in writing, software
            # distributed under the License is distributed on an "AS IS" BASIS,
            # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
            # See the License for the specific language governing permissions and
            # limitations under the License.
            
            import common
            import re
            import unittest
            
            class TestSequenceFunctions(unittest.TestCase):
              def test_insert_breaks(self):
                spaces = re.compile(r'\s+|\\n')
                def contains_all_chars(orig, result):
                  return re.sub(spaces, '', orig) == re.sub(spaces, '', result)
            
                sentences = [u'Quay Pegman qua bên trái hoặc bên phải 90 độ.',
                             u'Foo bar baz this is english that is okay bye.',
                             u'If there is a path in the specified direction, \nthen ' +
                             u'do some actions.',
                             u'If there is a path in the specified direction, then do ' +
                             u'the first block of actions. Otherwise, do the second ' +
                             u'block of actions.']
                for sentence in sentences:
                  output = common.insert_breaks(sentence, 30, 50)
                  self.assert_(contains_all_chars(sentence, output),
                               u'Mismatch between:\n{0}\n{1}'.format(
                                   re.sub(spaces, '', sentence),
                                   re.sub(spaces, '', output)))
            
            if __name__ == '__main__':
                unittest.main()
            ```
            </details>

          * `xliff_to_json.py`
            <details>
            <summary>View Content (Converted to Markdown)</summary>

            ```markdown
            #!/usr/bin/python
            
            # Converts .xlf files into .json files for use at http://translatewiki.net.
            #
            # Copyright 2013 Google Inc.
            # https://developers.google.com/blockly/
            #
            # Licensed under the Apache License, Version 2.0 (the "License");
            # you may not use this file except in compliance with the License.
            # You may obtain a copy of the License at
            #
            #   http://www.apache.org/licenses/LICENSE-2.0
            #
            # Unless required by applicable law or agreed to in writing, software
            # distributed under the License is distributed on an "AS IS" BASIS,
            # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
            # See the License for the specific language governing permissions and
            # limitations under the License.
            
            import argparse
            import os
            import re
            import subprocess
            import sys
            from xml.dom import minidom
            from common import InputError
            from common import write_files
            
            # Global variables
            args = None  # Parsed command-line arguments.
            
            def _parse_trans_unit(trans_unit):
                """Converts a trans-unit XML node into a more convenient dictionary format.
            
                Args:
                    trans_unit: An XML representation of a .xlf translation unit.
            
                Returns:
                    A dictionary with useful information about the translation unit.
                    The returned dictionary is guaranteed to have an entry for 'key' and
                    may have entries for 'source', 'target', 'description', and 'meaning'
                    if present in the argument.
            
                Raises:
                    InputError: A required field was not present.
                """
            
                def get_value(tag_name):
                    elts = trans_unit.getElementsByTagName(tag_name)
                    if not elts:
                        return None
                    elif len(elts) == 1:
                        return ''.join([child.toxml() for child in elts[0].childNodes])
                    else:
                        raise InputError('', 'Unable to extract ' + tag_name)
            
                result = {}
                key = trans_unit.getAttribute('id')
                if not key:
                    raise InputError('', 'id attribute not found')
                result['key'] = key
            
                # Get source and target, if present.
                try:
                    result['source'] = get_value('source')
                    result['target'] = get_value('target')
                except InputError, e:
                    raise InputError(key, e.msg)
            
                # Get notes, using the from value as key and the data as value.
                notes = trans_unit.getElementsByTagName('note')
                for note in notes:
                    from_value = note.getAttribute('from')
                    if from_value and len(note.childNodes) == 1:
                        result[from_value] = note.childNodes[0].data
                    else:
                        raise InputError(key, 'Unable to extract ' + from_value)
            
                return result
            
            def _process_file(filename):
                """Builds list of translation units from input file.
            
                Each translation unit in the input file includes:
                - an id (opaquely generated by Soy)
                - the Blockly name for the message
                - the text in the source language (generally English)
                - a description for the translator
            
                The Soy and Blockly ids are joined with a hyphen and serve as the
                keys in both output files.  The value is the corresponding text (in the
                <lang>.json file) or the description (in the qqq.json file).
            
                Args:
                    filename: The name of an .xlf file produced by Closure.
            
                Raises:
                    IOError: An I/O error occurred with an input or output file.
                    InputError: The input file could not be parsed or lacked required
                        fields.
            
                Returns:
                    A list of dictionaries produced by parse_trans_unit().
                """
                try:
                    results = []  # list of dictionaries (return value)
                    names = []    # list of names of encountered keys (local variable)
                    try:
                        parsed_xml = minidom.parse(filename)
                    except IOError:
                        # Don't get caught by below handler
                        raise
                    except Exception, e:
                        print
                        raise InputError(filename, str(e))
            
                    # Make sure needed fields are present and non-empty.
                    for trans_unit in parsed_xml.getElementsByTagName('trans-unit'):
                        unit = _parse_trans_unit(trans_unit)
                        for key in ['description', 'meaning', 'source']:
                            if not key in unit or not unit[key]:
                                raise InputError(filename + ':' + unit['key'],
                                                 key + ' not found')
                        if unit['description'].lower() == 'ibid':
                          if unit['meaning'] not in names:
                            # If the term has not already been described, the use of 'ibid'
                            # is an error.
                            raise InputError(
                                filename,
                                'First encountered definition of: ' + unit['meaning']
                                + ' has definition: ' + unit['description']
                                + '.  This error can occur if the definition was not'
                                + ' provided on the first appearance of the message'
                                + ' or if the source (English-language) messages differ.')
                          else:
                            # If term has already been described, 'ibid' was used correctly,
                            # and we output nothing.
                            pass
                        else:
                          if unit['meaning'] in names:
                            raise InputError(filename,
                                             'Second definition of: ' + unit['meaning'])
                          names.append(unit['meaning'])
                          results.append(unit)
            
                    return results
                except IOError, e:
                    print 'Error with file {0}: {1}'.format(filename, e.strerror)
                    sys.exit(1)
            
            def sort_units(units, templates):
                """Sorts the translation units by their definition order in the template.
            
                Args:
                    units: A list of dictionaries produced by parse_trans_unit()
                        that have a non-empty value for the key 'meaning'.
                    templates: A string containing the Soy templates in which each of
                        the units' meanings is defined.
            
                Returns:
                    A new list of translation units, sorted by the order in which
                    their meaning is defined in the templates.
            
                Raises:
                    InputError: If a meaning definition cannot be found in the
                        templates.
                """
                def key_function(unit):
                    match = re.search(
                        '\\smeaning\\s*=\\s*"{0}"\\s'.format(unit['meaning']),
                        templates)
                    if match:
                        return match.start()
                    else:
                        raise InputError(args.templates,
                                         'msg definition for meaning not found: ' +
                                         unit['meaning'])
                return sorted(units, key=key_function)
            
            def main():
                """Parses arguments and processes the specified file.
            
                Raises:
                    IOError: An I/O error occurred with an input or output file.
                    InputError: Input files lacked required fields.
                """
                # Set up argument parser.
                parser = argparse.ArgumentParser(description='Create translation files.')
                parser.add_argument(
                    '--author',
                    default='Ellen Spertus <ellen.spertus@gmail.com>',
                    help='name and email address of contact for translators')
                parser.add_argument('--lang', default='en',
                                    help='ISO 639-1 source language code')
                parser.add_argument('--output_dir', default='json',
                                    help='relative directory for output files')
                parser.add_argument('--xlf', help='file containing xlf definitions')
                parser.add_argument('--templates', default=['template.soy'], nargs='+',
                                    help='relative path to Soy templates, comma or space '
                                    'separated (used for ordering messages)')
                global args
                args = parser.parse_args()
            
                # Make sure output_dir ends with slash.
                if (not args.output_dir.endswith(os.path.sep)):
                  args.output_dir += os.path.sep
            
                # Process the input file, and sort the entries.
                units = _process_file(args.xlf)
                files = []
                for arg in args.templates:
                  for filename in arg.split(','):
                    filename = filename.strip();
                    if filename:
                      with open(filename) as myfile:
                        files.append(' '.join(line.strip() for line in myfile))
                sorted_units = sort_units(units, ' '.join(files))
            
                # Write the output files.
                write_files(args.author, args.lang, args.output_dir, sorted_units, True)
            
                # Delete the input .xlf file.
                os.remove(args.xlf)
                print('Removed ' + args.xlf)
            
            if __name__ == '__main__':
                main()
            ```
            </details>

        * **media/**
        * **msg/**
          * **js/**
          * **json/**
        * **tests/**
          * **blocks/**
          * **compile/**
          * **generators/**
          * **jsunit/**
          * **scripts/**
          * **workspace_svg/**
      * `dummy_robot.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        def begin():
            print("begin")
        
        def end():
            print("end")
        
        def forward(n=1):
            print("forward %d" %(n))
        
        def backward(n=1):
            print("backward %d" %(n))
        
        def left(n=1):
            print("left %d" %(n))
        
        def right(n=1):
            print("right %d" %(n))
        
        ```
        </details>

      * **img/**
  * **laser/**
    * `PyGLWidget.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      # -*- coding: utf-8 -*-
      #===============================================================================
      #
      # PyGLWidget.py
      #
      # A simple GL Viewer.
      #
      # Copyright (c) 2011, Arne Schmitz <arne.schmitz@gmx.net>
      # All rights reserved.
      #
      # Redistribution and use in source and binary forms, with or without
      # modification, are permitted provided that the following conditions are met:
      #     * Redistributions of source code must retain the above copyright
      #       notice, this list of conditions and the following disclaimer.
      #     * Redistributions in binary form must reproduce the above copyright
      #       notice, this list of conditions and the following disclaimer in the
      #       documentation and/or other materials provided with the distribution.
      #     * Neither the name of the <organization> nor the
      #       names of its contributors may be used to endorse or promote products
      #       derived from this software without specific prior written permission.
      #
      # THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
      # ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
      # WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
      # DISCLAIMED. IN NO EVENT SHALL <COPYRIGHT HOLDER> BE LIABLE FOR ANY
      # DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
      # (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
      # LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
      # ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
      # (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
      # SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
      #
      #===============================================================================
      
      try:
          from PyQt5 import QtCore, QtGui, QtOpenGL
          from PyQt5.QtWidgets import QApplication
          useQt5 = True
      except ImportError:
          from PyQt4 import QtCore, QtGui, QtOpenGL
          from PyQt4.QtGui import QApplication
          useQt5 = False
      import math
      import numpy
      import numpy.linalg as linalg
      import OpenGL
      OpenGL.ERROR_CHECKING = True
      from OpenGL.GL import *
      from OpenGL.GLU import *
      
      class PyGLWidget(QtOpenGL.QGLWidget):
      
          # Qt signals
          signalGLMatrixChanged = QtCore.pyqtSignal()
          rotationBeginEvent = QtCore.pyqtSignal()
          rotationEndEvent = QtCore.pyqtSignal()
      
          def __init__(self, parent = None):
              format = QtOpenGL.QGLFormat()
              format.setSampleBuffers(True)
              QtOpenGL.QGLWidget.__init__(self, format, parent)
              self.setCursor(QtCore.Qt.OpenHandCursor)
              self.setMouseTracking(True)
      
              self.modelview_matrix_  = []
              self.translate_vector_  = [0.0, 0.0, 0.0]
              self.viewport_matrix_   = []
              self.projection_matrix_ = []
              self.near_   = 0.1
              self.far_    = 100.0
              self.fovy_   = 45.0
              self.radius_ = 5.0
              self.last_point_2D_ = QtCore.QPoint()
              self.last_point_ok_ = False
              self.last_point_3D_ = [1.0, 0.0, 0.0]
              self.isInRotation_  = False
      
              # connections
              #self.signalGLMatrixChanged.connect(self.printModelViewMatrix)
      
          @QtCore.pyqtSlot()
          def printModelViewMatrix(self):
              print self.modelview_matrix_
      
          def initializeGL(self):
              # OpenGL state
              glClearColor(1.0, 1.0, 1.0, 0.0)
              glEnable(GL_DEPTH_TEST)
              self.reset_view()
      
          def resizeGL(self, width, height):
              glViewport( 0, 0, width, height );
              self.set_projection( self.near_, self.far_, self.fovy_ );
              self.updateGL()
      
          def paintGL(self):
              glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)
      
              glMatrixMode(GL_MODELVIEW)
              glLoadMatrixd(self.modelview_matrix_)
      
          def set_projection(self, _near, _far, _fovy):
              self.near_ = _near
              self.far_ = _far
              self.fovy_ = _fovy
              self.makeCurrent()
              glMatrixMode( GL_PROJECTION )
              glLoadIdentity()
              gluPerspective( self.fovy_, float(self.width()) / float(self.height()),
                              self.near_, self.far_ )
              self.updateGL()
      
          def set_center(self, _cog):
              self.center_ = _cog
              self.view_all()
      
          def set_radius(self, _radius):
              self.radius_ = _radius
              self.set_projection(_radius / 100.0, _radius * 100.0, self.fovy_)
              self.reset_view()
              self.translate([0, 0, -_radius * 2.0])
              self.view_all()
              self.updateGL()
      
          def reset_view(self):
              # scene pos and size
              glMatrixMode( GL_MODELVIEW )
              glLoadIdentity();
              self.modelview_matrix_ = glGetDoublev( GL_MODELVIEW_MATRIX )
              self.set_center([0.0, 0.0, 10.0])
      
          def reset_rotation(self):
              self.modelview_matrix_[0] = [1.0, 0.0, 0.0, 0.0]
              self.modelview_matrix_[1] = [0.0, 1.0, 0.0, 0.0]
              self.modelview_matrix_[2] = [0.0, 0.0, 1.0, 0.0]
              glMatrixMode(GL_MODELVIEW)
              glLoadMatrixd(self.modelview_matrix_)
              self.updateGL()
      
          def translate(self, _trans):
              # Translate the object by _trans
              # Update modelview_matrix_
              self.makeCurrent()
              glMatrixMode(GL_MODELVIEW)
              glLoadIdentity()
              glTranslated(_trans[0], _trans[1], _trans[2])
              glMultMatrixd(self.modelview_matrix_)
              self.modelview_matrix_ = glGetDoublev(GL_MODELVIEW_MATRIX)
              self.translate_vector_[0] = self.modelview_matrix_[3][0]
              self.translate_vector_[1] = self.modelview_matrix_[3][1]
              self.translate_vector_[2] = self.modelview_matrix_[3][2]
              self.signalGLMatrixChanged.emit()
      
          def rotate(self, _axis, _angle):
              t = [self.modelview_matrix_[0][0] * self.center_[0] +
                   self.modelview_matrix_[1][0] * self.center_[1] +
                   self.modelview_matrix_[2][0] * self.center_[2] +
                   self.modelview_matrix_[3][0],
                   self.modelview_matrix_[0][1] * self.center_[0] +
                   self.modelview_matrix_[1][1] * self.center_[1] +
                   self.modelview_matrix_[2][1] * self.center_[2] +
                   self.modelview_matrix_[3][1],
                   self.modelview_matrix_[0][2] * self.center_[0] +
                   self.modelview_matrix_[1][2] * self.center_[1] +
                   self.modelview_matrix_[2][2] * self.center_[2] +
                   self.modelview_matrix_[3][2]]
      
              self.makeCurrent()
              glLoadIdentity()
              glTranslatef(t[0], t[1], t[2])
              glRotated(_angle, _axis[0], _axis[1], _axis[2])
              glTranslatef(-t[0], -t[1], -t[2])
              glMultMatrixd(self.modelview_matrix_)
              self.modelview_matrix_ = glGetDoublev(GL_MODELVIEW_MATRIX)
              self.signalGLMatrixChanged.emit()
      
          def view_all(self):
              self.translate( [ -( self.modelview_matrix_[0][0] * self.center_[0] +
                                   self.modelview_matrix_[0][1] * self.center_[1] +
                                   self.modelview_matrix_[0][2] * self.center_[2] +
                                   self.modelview_matrix_[0][3]),
                                 -( self.modelview_matrix_[1][0] * self.center_[0] +
                                    self.modelview_matrix_[1][1] * self.center_[1] +
                                    self.modelview_matrix_[1][2] * self.center_[2] +
                                    self.modelview_matrix_[1][3]),
                                 -( self.modelview_matrix_[2][0] * self.center_[0] +
                                    self.modelview_matrix_[2][1] * self.center_[1] +
                                    self.modelview_matrix_[2][2] * self.center_[2] +
                                    self.modelview_matrix_[2][3] +
                                    self.radius_ / 2.0 )])
      
          def map_to_sphere(self, _v2D):
              _v3D = [0.0, 0.0, 0.0]
              # inside Widget?
              if (( _v2D.x() >= 0 ) and ( _v2D.x() <= self.width() ) and
                  ( _v2D.y() >= 0 ) and ( _v2D.y() <= self.height() ) ):
                  # map Qt Coordinates to the centered unit square [-0.5..0.5]x[-0.5..0.5]
                  x  = float( _v2D.x() - 0.5 * self.width())  / self.width()
                  y  = float( 0.5 * self.height() - _v2D.y()) / self.height()
      
                  _v3D[0] = x;
                  _v3D[1] = y;
                  # use Pythagoras to comp z-coord (the sphere has radius sqrt(2.0*0.5*0.5))
                  z2 = 2.0*0.5*0.5-x*x-y*y;
                  # numerical robust sqrt
                  _v3D[2] = math.sqrt(max( z2, 0.0 ))
      
                  # normalize direction to unit sphere
                  n = linalg.norm(_v3D)
                  _v3D = numpy.array(_v3D) / n
      
                  return True, _v3D
              else:
                  return False, _v3D
      
          def wheelEvent(self, _event):
              # Use the mouse wheel to zoom in/out
              global useQt5
              if (useQt5):
                  d = - float(_event.angleDelta().y()) / 200.0 * self.radius_
              else:
                   d = - float(_event.delta()) / 200.0 * self.radius_
              self.translate([0.0, 0.0, d])
              self.updateGL()
              _event.accept()
      
          def mousePressEvent(self, _event):
              self.last_point_2D_ = _event.pos()
              self.last_point_ok_, self.last_point_3D_ = self.map_to_sphere(self.last_point_2D_)
      
          def mouseMoveEvent(self, _event):
              newPoint2D = _event.pos()
      
              if ((newPoint2D.x() < 0) or (newPoint2D.x() > self.width()) or
                  (newPoint2D.y() < 0) or (newPoint2D.y() > self.height())):
                  return
      
              # Left button: rotate around center_
              # Middle button: translate object
              # Left & middle button: zoom in/out
      
              value_y = 0
              newPoint_hitSphere, newPoint3D = self.map_to_sphere(newPoint2D)
      
              dx = float(newPoint2D.x() - self.last_point_2D_.x())
              dy = float(newPoint2D.y() - self.last_point_2D_.y())
      
              w  = float(self.width())
              h  = float(self.height())
      
              # enable GL context
              self.makeCurrent()
      
              # move in z direction
              if (((_event.buttons() & QtCore.Qt.LeftButton) and (_event.buttons() & QtCore.Qt.MidButton))
                  or (_event.buttons() & QtCore.Qt.LeftButton and _event.modifiers() & QtCore.Qt.ControlModifier)):
                  value_y = self.radius_ * dy * 2.0 / h;
                  self.translate([0.0, 0.0, value_y])
              # move in x,y direction
              elif (_event.buttons() & QtCore.Qt.MidButton
                    or (_event.buttons() & QtCore.Qt.LeftButton and _event.modifiers() & QtCore.Qt.ShiftModifier)):
                  z = - (self.modelview_matrix_[0][2] * self.center_[0] +
                         self.modelview_matrix_[1][2] * self.center_[1] +
                         self.modelview_matrix_[2][2] * self.center_[2] +
                         self.modelview_matrix_[3][2]) / (self.modelview_matrix_[0][3] * self.center_[0] +
                                                          self.modelview_matrix_[1][3] * self.center_[1] +
                                                          self.modelview_matrix_[2][3] * self.center_[2] +
                                                          self.modelview_matrix_[3][3])
      
                  fovy   = 45.0
                  aspect = w / h
                  n      = 0.01 * self.radius_
                  up     = math.tan(fovy / 2.0 * math.pi / 180.0) * n
                  right  = aspect * up
      
                  self.translate( [2.0 * dx / w * right / n * z,
                                   -2.0 * dy / h * up / n * z,
                                   0.0] )
      
              # rotate
              elif (_event.buttons() & QtCore.Qt.LeftButton):
                  if (not self.isInRotation_):
                      self.isInRotation_ = True
                      self.rotationBeginEvent.emit()
      
                  axis = [0.0, 0.0, 0.0]
                  angle = 0.0
      
                  if (self.last_point_ok_ and newPoint_hitSphere):
                      axis = numpy.cross(self.last_point_3D_, newPoint3D)
                      cos_angle = numpy.dot(self.last_point_3D_, newPoint3D)
                      if (abs(cos_angle) < 1.0):
                          angle = math.acos(cos_angle) * 180.0 / math.pi
                          angle *= 2.0
                      self.rotate(axis, angle)
      
              # remember this point
              self.last_point_2D_ = newPoint2D
              self.last_point_3D_ = newPoint3D
              self.last_point_ok_ = newPoint_hitSphere
      
              # trigger redraw
              self.updateGL()
      
              def mouseReleaseEvent(self, _event):
                  if (isInRotation_):
                      isInRotation_ = false
                      self.rotationEndEvent.emit()
                  last_point_ok_ = False
      
      #===============================================================================
      #
      # Local Variables:
      # mode: Python
      # indent-tabs-mode: nil
      # End:
      #
      #===============================================================================
      ```
      </details>

    * `getlaser.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/core/almemory-api.html
      #http://doc.aldebaran.com/2-5/family/pepper_technical/pepper_dcm/actuator_sensor_names.html#ju-lasers
      
      import qi
      import argparse
      import sys
      import time
      import threading
      import os
      
      laserValueList = [
        # RIGHT LASER
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg01/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg01/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg02/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg02/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg03/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg03/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg04/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg04/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg05/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg05/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg06/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg06/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg07/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg07/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg08/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg08/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg09/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg09/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg10/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg10/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg11/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg11/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg12/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg12/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg13/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg13/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg14/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg14/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg15/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg15/Y/Sensor/Value",
        # FRONT LASER
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg01/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg01/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg02/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg02/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg03/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg03/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg04/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg04/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg05/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg05/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg06/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg06/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg07/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg07/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg08/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg08/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg09/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg09/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg10/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg10/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg11/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg11/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg12/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg12/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg13/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg13/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg14/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg14/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg15/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg15/Y/Sensor/Value",
        # LEFT LASER
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg01/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg01/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg02/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg02/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg03/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg03/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg04/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg04/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg05/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg05/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg06/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg06/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg07/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg07/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg08/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg08/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg09/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg09/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg10/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg10/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg11/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg11/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg12/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg12/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg13/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg13/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg14/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg14/Y/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg15/X/Sensor/Value",
        "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg15/Y/Sensor/Value"
      ]
      
      import threading
      
      def rhMonitorThread (memory_service):
          t = threading.currentThread()
          while getattr(t, "do_run", True):
              laserValues =  memory_service.getListData(laserValueList)
              print laserValues[44],laserValues[45] #X,Y values of central point
              time.sleep(0.1)
          print "Exiting Thread"
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["laserReader", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          memory_service  = session.service("ALMemory")
      
          #create a thead that monitors directly the signal
          monitorThread = threading.Thread(target = rhMonitorThread, args = (memory_service,))
          monitorThread.start()
      
          #Program stays at this point until we stop it
          app.run()
      
          monitorThread.do_run = False
      
          print "Finished"
      
      if __name__ == "__main__":
          main()
      ```
      </details>

    * `laserviewer.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/core/almemory-api.html
      #http://doc.aldebaran.com/2-5/family/pepper_technical/pepper_dcm/actuator_sensor_names.html#ju-lasers
      
      import qi
      import argparse
      import sys
      import time
      import threading
      import os
      
      laserValueList = [
          # RIGHT LASER
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg01/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg01/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg02/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg02/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg03/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg03/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg04/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg04/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg05/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg05/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg06/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg06/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg07/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg07/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg08/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg08/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg09/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg09/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg10/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg10/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg11/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg11/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg12/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg12/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg13/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg13/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg14/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg14/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg15/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Right/Horizontal/Seg15/Y/Sensor/Value",
          # FRONT LASER
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg01/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg01/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg02/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg02/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg03/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg03/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg04/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg04/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg05/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg05/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg06/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg06/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg07/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg07/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg08/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg08/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg09/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg09/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg10/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg10/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg11/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg11/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg12/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg12/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg13/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg13/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg14/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg14/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg15/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Front/Horizontal/Seg15/Y/Sensor/Value",
          # LEFT LASER
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg01/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg01/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg02/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg02/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg03/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg03/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg04/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg04/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg05/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg05/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg06/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg06/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg07/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg07/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg08/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg08/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg09/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg09/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg10/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg10/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg11/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg11/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg12/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg12/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg13/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg13/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg14/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg14/Y/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg15/X/Sensor/Value",
          "Device/SubDeviceList/Platform/LaserSensor/Left/Horizontal/Seg15/Y/Sensor/Value"
      ]
      
      import threading
      import math
      try:
          from PyQt5 import QtCore, QtGui, QtOpenGL
          from PyQt5.QtWidgets import QApplication
      except ImportError:
          from PyQt4 import QtCore, QtGui, QtOpenGL
          from PyQt4.QtGui import QApplication
      from OpenGL.GL import *
      from PyGLWidget import PyGLWidget
      
      MAX_RANGE = 5.0
      class LaserViewer(PyGLWidget):
      
          def __init__(self, parent = None):
              PyGLWidget.__init__(self, parent)
              self.laserpoints = []
              self.running = True
      
          def paintGL(self):
              PyGLWidget.paintGL(self)
              glPushMatrix()
              glPushAttrib(GL_COLOR|GL_POINT_SIZE)
      
              glPointSize(15)
              glBegin(GL_POINTS)
              glColor3f(0.0, 0.0, 0.0)
              glVertex3f(0, 0, 0)
              glEnd()
      
              glPointSize(5)
              glBegin(GL_POINTS)
              for i in range(0,len(self.laserpoints)):
                  point = self.laserpoints[i]
                  d = math.sqrt(point[0]*point[0]+point[1]*point[1])
                  if d >= MAX_RANGE:
                      continue
                  if (i<15): # right
                      glColor3f(0.0, 1.0, 0.0)
                  elif (i<30): # front
                      glColor3f(1.0, 0.0, 0.0)
                  else: # left
                      glColor3f(0.0, 0.0, 1.0)
                  glVertex3f(point[0], point[1], 0)
              glEnd()
      
              glPopAttrib()
              glPopMatrix()
      
          def setLaserPoints(self, laserpoints):
              self.laserpoints = laserpoints
      
          def closeEvent(self, event):
              self.running = False
      
          def isRunning(self):
              return self.running
      
      def laserMonitorThread (memory_service, laserviewer):
          t = threading.currentThread()
          while getattr(t, "do_run", True):
              laserValues =  memory_service.getListData(laserValueList)
              rawPoints = []
              for i in range(0,len(laserValues),2):
                  rawPoints.append((laserValues[i],laserValues[i+1]))
      
              pointsInRobotFrame = rawPointsToRobotFrame(rawPoints)
              laserviewer.setLaserPoints(pointsInRobotFrame)
      
              time.sleep(0.2)
      
          print "Exiting Thread"
      
      def rawPointsToRobotFrame (rawPoints):
          pointsInRobotFrame = [0]*len(rawPoints)
          for i in range(0,len(rawPoints)):
              rawPoint = rawPoints[i]
              if i < 15:
                  #Right Laser Point
                  lX = -0.01800; lY = -0.08990; lt = -1.57079 + math.pi/2;
                  tX = rawPoint[0]*math.cos(lt) - rawPoint[1]*math.sin(lt) + lX
                  tY = rawPoint[0]*math.sin(lt) + rawPoint[1]*math.cos(lt) + lY
                  pointsInRobotFrame[i] = (tX,tY)
                  continue
              if i < 30:
                  #Front Laser Point
                  lX = 0.05620; lY = 0.0; lt = 0.0 + math.pi/2;
                  tX = rawPoint[0]*math.cos(lt) - rawPoint[1]*math.sin(lt) + lX
                  tY = rawPoint[0]*math.sin(lt) + rawPoint[1]*math.cos(lt) + lY
                  pointsInRobotFrame[i] = (tX,tY)
                  continue
              if i < 45:
                  #Left Laser Point
                  lX = -0.01800; lY = 0.08990; lt = 1.57079 + math.pi/2;
                  tX = rawPoint[0]*math.cos(lt) - rawPoint[1]*math.sin(lt) + lX
                  tY = rawPoint[0]*math.sin(lt) + rawPoint[1]*math.cos(lt) + lY
                  pointsInRobotFrame[i] = (tX,tY)
                  continue
      
          return pointsInRobotFrame
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["laserReader", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
      
          session = app.session
      
          #Starting Qt Application
          qapp = QApplication(["Pepper Laser Viewer"])
          laserviewer = LaserViewer()
          laserviewer.show()
          laserviewer.raise_()
      
          #Starting services
          memory_service  = session.service("ALMemory")
      
          #create a thead that monitors directly the signal
          monitorThread = threading.Thread(target = laserMonitorThread, args = (memory_service,laserviewer,))
          monitorThread.start()
          while (laserviewer.isRunning()):
              qapp.processEvents()
              laserviewer.updateGL()
      
          monitorThread.do_run = False
      
          print "Finished"
      
      if __name__ == "__main__":
          main()
      ```
      </details>

  * **leds/**
    * `leds.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      import qi
      import argparse
      import sys
      import time
      import os
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--values", type=str, default='[0.00, -0.21, 1.55, 0.13, -1.24, -0.52, 0.01, 1.56, -0.14, 1.22, 0.52, -0.01]',
                              help="Joint values")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          values = eval(args.values)
      
          #Starting session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          leds_service = session.service("ALLeds")
      
          time.sleep(3)
      
          leds_service.on('FaceLeds')
      
          sys.exit(1)
      
          leds_service.randomEyes(5)
          time.sleep(1)
          leds_service.rasta(5)
          time.sleep(1)
          leds_service.rotateEyes(0x00802020, 1, 5)
          time.sleep(1)
          leds_service.off('AllLeds')
          time.sleep(1)
          leds_service.on('AllLeds')
          time.sleep(1)
          leds_service.reset('AllLeds')
      
      if __name__ == "__main__":
      
          main()
      
      ```
      </details>

  * **memory/**
    * `raiseEvent.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      
      
      import qi
      import argparse
      import sys
      import os
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--event", type=str, default="event_test",
                              help="name of the event to raise")
          parser.add_argument("--data", type=str, default="data_event_test",
                              help="data to send")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          event = args.event
          data = args.data
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["ReactToTouch", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          memory_service  = session.service("ALMemory")
      
          #subscribe to any change on any touch sensor
          memory_service.raiseEvent(event, data)
      
          print "Finished"
      
      if __name__ == "__main__":
          main()
      ```
      </details>

    * `read.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #!/usr/bin/env python
      
      import qi
      import argparse
      import sys
      import time
      import threading
      import os
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--key", type=str, default="Dialog/MyRobotName",
                              help="Memory key to read")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          key = args.key
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["Memory Read", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          memory_service  = session.service("ALMemory")
      
          try:
              val = memory_service.getData(key)
              print "Read ",key," = ",val
          except:
              print "Key ",key," NOT PRESENT"
      
      if __name__ == "__main__":
          main()
      
      ```
      </details>

    * `subscribeEvent.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      
      import qi
      import argparse
      import sys
      import os
      
      def onEvent(value):
          print "value=",value
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--event", type=str, default="event_test",
                              help="name of the event to subscribe")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          event = args.event
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["ReactToTouch", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          memory_service  = session.service("ALMemory")
      
          #subscribe to any change on any touch sensor
          subscriber = memory_service.subscriber(event)
          idEvent = subscriber.signal.connect(onEvent)
      
          #Program stays at this point until we stop it
          app.run()
      
          #Disconnecting callbacks
          subscriber.signal.disconnect(idEvent)
      
          print "Finished"
      
      if __name__ == "__main__":
          main()
      ```
      </details>

    * `write.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #!/usr/bin/env python
      
      import qi
      import argparse
      import sys
      import time
      import threading
      import os
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--key", type=str, default="memory_test",
                              help="Memory key to write")
          parser.add_argument("--val", type=str, default="something",
                              help="Memory value to write")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          key = args.key
          val = args.val
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              print "Connecting to ",	connection_url
              app = qi.Application(["Memory Write", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          memory_service  = session.service("ALMemory")
      
          try:
              val = float(val)
          except:
              pass
      
          memory_service.insertData(key,val)
      
          print "Write ",key," = ",val
      
      if __name__ == "__main__":
          main()
      
      ```
      </details>

  * **motion/**
    * `move.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/motion/control-walk-api.html
      
      import qi
      import argparse
      import sys
      import time
      import math
      import os
      
      motion_service = None
      
      def init():
          global motion_service
      
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          print "Connecting to tcp://" + pip + ":" + str(pport)
      
      	#Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["Move", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          motion_service = session.service("ALMotion")
      
      def setSpeed(lin_vel,ang_vel,dtime):
          global motion_service
          motion_service.move(lin_vel,0,ang_vel)
          time.sleep(dtime)
          motion_service.stopMove()
      
      def square(r):
          if (r>0.1):
              print 'Square ',r
              v = 0.3
              w = 0.7
              for i in range(0,4):
                  setSpeed(v,0,r/v)
                  setSpeed(0,w,(math.pi/2)/w)
      
      def circle(r):
          if (r>0.1):
              print 'Circle ',r
      	v = 0.3
      	dt = 2*math.pi*r/v
      	w = 2*math.pi/dt # = v/r
      	setSpeed(v,w,dt)
      
      def forward(r=1):
          print 'Forward ',r
          s = 0.5*r
          v = 0.2
          setSpeed(v,0,abs(s/v))
      
      def backward(r=1):
          print 'Backward ',r
          s = 0.5*r
          v = -0.2
          setSpeed(v,0,abs(s/v))
      
      def left(r=1):
          print 'Left ',r
          s = (math.pi/2)*r
          w = 0.5
          setSpeed(0,w,abs(s/w))
      
      def right(r=1):
          print 'Right ',r
          s = (math.pi/2)*r
          w = -0.5
          setSpeed(0,w,abs(s/w))
      
      def main():
      
          init()
      
          forward()
          left()
          right(2)
          left()
          backward()
      
          #square(0.5)
          #circle(0)
      
      if __name__ == "__main__":
          main()
      
      ```
      </details>

    * `moveSquare.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/motion/control-walk-api.html
      
      import qi
      import argparse
      import sys
      import time
      import math
      import os
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Start working session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          #Starting services
          motion_service = session.service("ALMotion")
      
          #Example of how to get robot position in world.
          useSensorValues = False
          result = motion_service.getRobotPosition(useSensorValues)
          print "Initial Robot Position", result
      
          #This sequence of commands follow a square of 1m size
          #Move 1m in its X direction
          x = 1.0
          y = 0.0
          theta = 0.0
          motion_service.moveTo(x, y, theta) #blocking function
      
          #Turn 90deg to the left
          x = 0.0
          y = 0.0
          theta = math.pi/2
          motion_service.moveTo(x, y, theta) #blocking function
      
          #Move 1m in its X direction
          x = 1.0
          y = 0.0
          theta = 0.0
          motion_service.moveTo(x, y, theta) #blocking function
      
          #Turn 90deg to the left
          x = 0.0
          y = 0.0
          theta = math.pi/2
          motion_service.moveTo(x, y, theta) #blocking function
      
          #Move at 0.2m/s in the X direction during 5 seconds (=1m)
          x = 0.2;
          y = 0.0;
          theta = 0.0;
          motion_service.move(x, y, theta) #non-blocking function, Pepper will move forever!!
      
          time.sleep(5)
          motion_service.stopMove() #stopping previous move
      
          #Turn 90deg to the left
          x = 0.0
          y = 0.0
          theta = math.pi/2
          motion_service.moveTo(x, y, theta) #blocking function
      
          #Move 1m in its X direction
          x = 1.0
          y = 0.0
          theta = 0.0
          motion_service.moveTo(x, y, theta) #blocking function
      
          #Turn 90deg to the left
          x = 0.0
          y = 0.0
          theta = math.pi/2
          motion_service.moveTo(x, y, theta) #blocking function
      
          #time = 5.0
          #motion_service.moveTo(x, y, theta, time) #blocking function
      
          result = motion_service.getRobotPosition(useSensorValues)
          print "Final Robot Position", result
      
      if __name__ == "__main__":
          main()
      ```
      </details>

    * `pepper_joystick.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/core/almemory-api.html
      #http://doc.aldebaran.com/2-5/naoqi/sensors/altouch-api.html
      #http://doc.aldebaran.com/2-5/dev/libqi/api/python/signal.html
      #http://doc.aldebaran.com/2-5/family/pepper_technical/pepper_dcm/actuator_sensor_names.html
      #http://doc.aldebaran.com/2-5/naoqi/motion/control-walk-api.html
      
      import qi
      import argparse
      import sys
      import time
      import functools
      import threading
      import os
      
      sonarMemoryValue = "Device/SubDeviceList/Platform/Front/Sonar/Sensor/Value"
      safeDistance = 0.75
      movingForward = False
      
      def rhMonitorThread (memory_service, motion_service):
          global movingForward
          t = threading.currentThread()
          print "Monitoring Front Sonar Active"
          while getattr(t, "do_run", True):
              sonarValue =  memory_service.getData(sonarMemoryValue)
              if (sonarValue < safeDistance and movingForward):
                  print "[Front]", sonarValue
                  print "Obstacle detected!! Stopping robot"
                  motion_service.stopMove()
      
              time.sleep(.2)
      
          print "Monitoring Front Sonar Finished"
          print "Exiting Thread"
      
      def onHeadFrontTouched(motion_service, value):
          print "Head Front value=",value
      
          global movingForward
          if value == 1.0:
              print "Moving Forward."
              x = 0.2
              y = 0.0
              theta = 0.0
              movingForward = True
              motion_service.move(x, y, theta)
          else:
              print "Stopping."
              movingForward = False
              motion_service.stopMove()
      
      def onHeadRearTouched(motion_service, value):
          print "Head Rear value=",value
      
          if value == 1.0:
              print "Moving Backward."
              x = -0.1
              y = 0.0
              theta = 0.0
              motion_service.move(x, y, theta)
          else:
              print "Stoping."
              motion_service.stopMove()
      
      def onHandRightTouched(motion_service, value):
          print "Hand Right value=",value
      
          if value == 1.0:
              print "Turning Right."
              x = 0.0
              y = 0.0
              theta = -0.5
              motion_service.move(x, y, theta)
          else:
              print "Stoping."
              motion_service.stopMove()
      
      def onHandLeftTouched(motion_service, value):
          print "Hand Left value=",value
      
          if value == 1.0:
              print "Turning Left."
              x = 0.0
              y = 0.0
              theta = 0.5
              motion_service.move(x, y, theta)
          else:
              print "Stoping."
              motion_service.stopMove()
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["ReactToTouch", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          memory_service  = session.service("ALMemory")
          motion_service = session.service("ALMotion")
      
          motion_service.setExternalCollisionProtectionEnabled("Move", False)
      
          #subscribe to any change on "FrontTactilTouched" touch sensor
          headFrontTouched = memory_service.subscriber("FrontTactilTouched")
          idHeadFrontTouch = headFrontTouched.signal.connect(functools.partial(onHeadFrontTouched, motion_service))
      
          #subscribe to any change on "RearTactilTouched" touch sensor
          headRearTouched = memory_service.subscriber("RearTactilTouched")
          idHeadRearTouch = headRearTouched.signal.connect(functools.partial(onHeadRearTouched, motion_service))
      
          #subscribe to any change on "HandRightBackTouched" touch sensor
          handRightTouched = memory_service.subscriber("HandRightBackTouched")
          idHandRightTouch = handRightTouched.signal.connect(functools.partial(onHandRightTouched, motion_service))
      
          #subscribe to any change on "HandLeftBackTouched" touch sensor
          handLeftTouched = memory_service.subscriber("HandLeftBackTouched")
          idHandLeftTouch = handLeftTouched.signal.connect(functools.partial(onHandLeftTouched, motion_service))
      
          #create a thead that monitors directly the signal
          monitorThread = threading.Thread(target = rhMonitorThread, args = (memory_service, motion_service,))
          monitorThread.start()
      
          #Program stays at this point until we stop it
          app.run()
      
          #Disconnecting callbacks and Threads
          headFrontTouched.signal.disconnect(idHeadFrontTouch)
          headRearTouched.signal.disconnect(idHeadRearTouch)
          handRightTouched.signal.disconnect(idHandRightTouch)
          handLeftTouched.signal.disconnect(idHandLeftTouch)
          monitorThread.do_run = False
      
          motion_service.setExternalCollisionProtectionEnabled("Move", True)
      
          motion_service.stopMove()
      
          print "Finished"
      
      if __name__ == "__main__":
          main()
      ```
      </details>

    * `stop.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/motion/control-walk-api.html
      
      import qi
      import argparse
      import sys
      import time
      import math
      import os
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          print "Connecting to tcp://" + pip + ":" + str(pport)
      
      	#Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["Move", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          motion_service = session.service("ALMotion")
      
          print 'Stop'
      
          motion_service.stopMove()
      
      if __name__ == "__main__":
          main()
      
      ```
      </details>

  * **say/**
    * `asay.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      import qi
      import argparse
      import sys
      import time
      import os
      
      def getenv(envstr, def=None):
          if envstr in os.environ:
              return os.environ[envstr]
          else:
              return def
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=getenv('PEPPER_IP','127.0.0.1'),
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=getenv('PEPPER_PORT',9559),
                              help="Naoqi port number (default: 9559)")
          parser.add_argument("--sentence", type=str, default="hello",
                              help="Sentence to say")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          strsay = args.sentence
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["Say", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          ans_service = session.service("ALAnimatedSpeech")
          configuration = {"bodyLanguageMode":"contextual"}
      
          ans_service.say(strsay, configuration)
          print "  -- Animated Say: "+strsay
      
      if __name__ == "__main__":
          main()
      ```
      </details>

    * `say.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      import qi
      import argparse
      import sys
      import time
      import os
      
      def getenv(envstr, def=None):
          if envstr in os.environ:
              return os.environ[envstr]
          else:
              return def
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=getenv('PEPPER_IP','127.0.0.1'),
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=getenv('PEPPER_PORT',9559),
                              help="Naoqi port number (default: 9559)")
          parser.add_argument("--sentence", type=str, default="hello",
                              help="Sentence to say")
          parser.add_argument("--language", type=str, default="English",
                              help="language")
          parser.add_argument("--speed", type=int, default=100,
                              help="speed")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          strsay = args.sentence
          language = args.language
          speed = args.speed
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["Say", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          tts_service = session.service("ALTextToSpeech")
      
          tts_service.setLanguage(language)
          tts_service.setVolume(1.0)
          tts_service.setParameter("speed", speed)
          tts_service.say(strsay)
          print "  -- Say: "+strsay
      
      if __name__ == "__main__":
          main()
      ```
      </details>

  * **setjointangle/**
    * `headscan.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      
      import qi
      import argparse
      import sys
      import time
      import os
      
      jointNames = ["HeadYaw", "HeadPitch"]
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Start working session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          motion_service  = session.service("ALMotion")
      
          #we initialize pose of head looking left
          initAngles = [1.6, -0.2]
          timeLists  = [5.0, 5.0]
          isAbsolute = True
          motion_service.angleInterpolation(jointNames, initAngles, timeLists, isAbsolute)
      
          #we move head to look right
          finalAngles = [-1.6, -0.2]
          timeLists  = [10.0, 10.0]
          motion_service.angleInterpolation(jointNames, finalAngles, timeLists, isAbsolute)
      
          #we move head to center
          finalAngles = [0.0, -0.2]
          timeLists  = [5.0, 5.0]
          motion_service.angleInterpolation(jointNames, finalAngles, timeLists, isAbsolute)
      
      if __name__ == "__main__":
          main()
      ```
      </details>

    * `playPosture.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      import qi
      import argparse
      import sys
      import time
      import os
      
      jointsNames = ["HeadYaw", "HeadPitch",
                     "LShoulderPitch", "LShoulderRoll", "LElbowYaw", "LElbowRoll", "LWristYaw",
                     "RShoulderPitch", "RShoulderRoll", "RElbowYaw", "RElbowRoll", "RWristYaw"]
      
      #joint limits taken from http://doc.aldebaran.com/2-5/family/pepper_technical/joints_pep.html
      jointLimits ={'HeadYaw': (-2.0857, 2.0857),
                    'HeadPitch': (-0.7068, 0.6371),
                    'LShoulderPitch': (-2.0857, 2.0857),
                    'LShoulderRoll': (0.0087, 1.5620),
                    'LElbowYaw': (-2.0857, 2.0857),
                    'LElbowRoll': (-1.5620, -0.0087),
                    'LWristYaw': (-1.8239, 1.8239),
                    'RShoulderPitch': (-2.0857, 2.0857),
                    'RShoulderRoll': (-1.5620, -0.0087),
                    'RElbowYaw': (-2.0857, 2.0857),
                    'RElbowRoll': (0.0087,1.5620),
                    'RWristYaw': (-1.8239, 1.8239)}
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--values", type=str, default='[0.00, -0.21, 1.55, 0.13, -1.24, -0.52, 0.01, 1.56, -0.14, 1.22, 0.52, -0.01]',
                              help="Joint values")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          jointValues = eval(args.values)
      
          if (jointValues==None):
              print 'No joint values.'
              sys.exit(0)
      
          #Starting session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          print "Set joint values: ", jointValues
          isAbsolute = True
      
          #Starting services
          motion_service  = session.service("ALMotion")
          motion_service.angleInterpolation(jointsNames, jointValues, 3.0, isAbsolute)
      
      if __name__ == "__main__":
      
          main()
      ```
      </details>

    * `readPosture.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      import qi
      import argparse
      import sys
      import time
      import os
      
      jointsNames = ["HeadYaw", "HeadPitch",
                     "LShoulderPitch", "LShoulderRoll", "LElbowYaw", "LElbowRoll", "LWristYaw",
                     "RShoulderPitch", "RShoulderRoll", "RElbowYaw", "RElbowRoll", "RWristYaw"]
      
      #joint limits taken from http://doc.aldebaran.com/2-5/family/pepper_technical/joints_pep.html
      jointLimits ={'HeadYaw': (-2.0857, 2.0857),
                    'HeadPitch': (-0.7068, 0.6371),
                    'LShoulderPitch': (-2.0857, 2.0857),
                    'LShoulderRoll': (0.0087, 1.5620),
                    'LElbowYaw': (-2.0857, 2.0857),
                    'LElbowRoll': (-1.5620, -0.0087),
                    'LWristYaw': (-1.8239, 1.8239),
                    'RShoulderPitch': (-2.0857, 2.0857),
                    'RShoulderRoll': (-1.5620, -0.0087),
                    'RElbowYaw': (-2.0857, 2.0857),
                    'RElbowRoll': (0.0087,1.5620),
                    'RWristYaw': (-1.8239, 1.8239)}
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          #Starting services
          motion_service  = session.service("ALMotion")
          useSensors = True
          jointValues = motion_service.getAngles(jointsNames, useSensors)
      
          print "Read joint values: ", jointValues
      
      if __name__ == "__main__":
      
          main()
      ```
      </details>

    * `recordPostureGUI.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      from Tkinter import *
      
      jointsNames = ["HeadYaw", "HeadPitch",
                     "LShoulderPitch", "LShoulderRoll", "LElbowYaw", "LElbowRoll", "LWristYaw",
                     "RShoulderPitch", "RShoulderRoll", "RElbowYaw", "RElbowRoll", "RWristYaw"]
      
      #joint limits taken from http://doc.aldebaran.com/2-5/family/pepper_technical/joints_pep.html
      jointLimits ={'HeadYaw': (-2.0857, 2.0857),
                    'HeadPitch': (-0.7068, 0.6371),
                    'LShoulderPitch': (-2.0857, 2.0857),
                    'LShoulderRoll': (0.0087, 1.5620),
                    'LElbowYaw': (-2.0857, 2.0857),
                    'LElbowRoll': (-1.5620, -0.0087),
                    'LWristYaw': (-1.8239, 1.8239),
                    'RShoulderPitch': (-2.0857, 2.0857),
                    'RShoulderRoll': (-1.5620, -0.0087),
                    'RElbowYaw': (-2.0857, 2.0857),
                    'RElbowRoll': (0.0087,1.5620),
                    'RWristYaw': (-1.8239, 1.8239)}
      
      postures = {}
      class JointAnglesGUI:
      
          def __init__(self, master, motion_service, posture_service):
              self.master = master
              master.title("Joint Angles GUI")
              self.motion_service = motion_service
              self.posture_service = posture_service
      
              useSensors = True
      
              print "Joints sensors readings:"
              self.labels = []
              self.scalewidgets = []
              initSensorAngles = self.motion_service.getAngles(jointsNames, useSensors)
              for j, jointname in enumerate(jointsNames):
                  #Reading initial angles
                  print (jointname, str(initSensorAngles[j]))
      
                  self.labels.append(Label(text=jointname))
                  self.labels[j].grid(row=j, column=1)
                  self.scalewidgets.append(Scale(master, from_=jointLimits[jointname][0], to=jointLimits[jointname][1], resolution=0.1, orient="horizontal", length=200))
                  self.scalewidgets[j].set(float(initSensorAngles[j]))
                  self.scalewidgets[j].bind("<ButtonRelease-1>", lambda event, widget = self.scalewidgets[j], jointname = jointsNames[j]: self.updateValue(event, widget, jointname))
                  self.scalewidgets[j].grid(row=j, column=2)
      
              namePosture = 'Init'
              postures[namePosture]=initSensorAngles
      
              self.buttonreset = Button(self.master, text="Reset", command=self.callbackReset)
              self.buttonreset.grid(row=len(jointsNames)+1, column=1)
              self.buttonsave = Button(self.master, text="Save", command=self.callbackSave)
              self.buttonsave.grid(row=len(jointsNames)+1, column=2)
      
              self.labelposture = Label(text="Select Posture")
              self.labelposture.grid(row=len(jointsNames)+2,column=1)
              self.posturesmenu_var = StringVar(self.master)
              self.posturesmenu_var.set(postures.keys()[0])
              self.posturesmenu = OptionMenu(self.master, self.posturesmenu_var, postures.keys()[0], command = self.gotoPostureSelected)
              self.posturesmenu.configure(width=20)
              self.posturesmenu.grid(row=len(jointsNames)+2,column=2)
      
          def updateValue(self, event, widget, jointname):
              print "setting", jointname, "to: ", widget.get()
              angles = widget.get()
              fractionMaxSpeed = 0.1
              self.motion_service.setAngles(jointname,angles,fractionMaxSpeed)
              time.sleep(0.5)
      
          def callbackReset(self):
              print "click!"
              #Use a ALRobotPosture to go to posture "Stand"
              self.posture_service.goToPosture("Stand",1.0)
              for j, jointname in enumerate(jointsNames):
                  angle = self.motion_service.getAngles(jointname, True)
                  self.scalewidgets[j].set(angle[0])
      
          def callbackSave(self):
              print "click!"
              #Read angles of all joints
              savedPosture = self.motion_service.getAngles(jointsNames, True)
              print savedPosture
              #Storing posture
              namePosture = 'Posture'+str(len(postures))
              postures[namePosture]=savedPosture
              print postures.keys()
              self.updatePosturesMenu(namePosture)
      
          def updatePosturesMenu(self, posture):
              menu=self.posturesmenu['menu']
              menu.add_command(label=posture, command=lambda v=posture: self.gotoPostureSelected(v))
      
          def gotoPostureSelected(self, posture):
              self.posturesmenu_var.set(posture)
              print self.posturesmenu_var.get()
              print "doing something"
      
              isAbsolute = True
              self.motion_service.angleInterpolation(jointsNames, postures[posture], 1.0, isAbsolute)
      
      import qi
      import argparse
      import sys
      import time
      import os
      
      class AngleController:
          def __init__(self,master):
              self.master = master
              parser = argparse.ArgumentParser()
              parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                                  help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
              parser.add_argument("--pport", type=int, default=9559,
                                  help="Naoqi port number")
              args = parser.parse_args()
              pip = args.pip
              pport = args.pport
      
              #Starting session
              session = qi.Session()
              try:
                  session.connect("tcp://" + pip + ":" + str(pport))
              except RuntimeError:
                  print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                         "Please check your script arguments. Run with -h option for help.")
                  sys.exit(1)
      
              #Starting services
              self.motion_service  = session.service("ALMotion")
              self.posture_service = session.service("ALRobotPosture")
      
              gui = JointAnglesGUI(self.master, self.motion_service, self.posture_service)
              master.mainloop()
      
      if __name__ == "__main__":
          master = Tk()
          ac = AngleController(master)
      ```
      </details>

    * `recordPostureGUI_exercise.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #MODULES USED IN THIS PROGRAM
      #http://doc.aldebaran.com/2-5/naoqi/motion/alrobotposture-api.html
      #http://doc.aldebaran.com/2-5/naoqi/motion/control-joint-api.html
      
      from Tkinter import *
      import qi
      import argparse
      import sys
      import time
      import os
      
      jointsNames = ["HeadYaw", "HeadPitch",
                     "LShoulderPitch", "LShoulderRoll", "LElbowYaw", "LElbowRoll", "LWristYaw",
                     "RShoulderPitch", "RShoulderRoll", "RElbowYaw", "RElbowRoll", "RWristYaw"]
      
      #joint limits for Pepper, taken from http://doc.aldebaran.com/2-5/family/pepper_technical/joints_pep.html
      jointLimits ={'HeadYaw': (-2.0857, 2.0857),
                    'HeadPitch': (-0.7068, 0.6371),
                    'LShoulderPitch': (-2.0857, 2.0857),
                    'LShoulderRoll': (0.0087, 1.5620),
                    'LElbowYaw': (-2.0857, 2.0857),
                    'LElbowRoll': (-1.5620, -0.0087),
                    'LWristYaw': (-1.8239, 1.8239),
                    'RShoulderPitch': (-2.0857, 2.0857),
                    'RShoulderRoll': (-1.5620, -0.0087),
                    'RElbowYaw': (-2.0857, 2.0857),
                    'RElbowRoll': (0.0087,1.5620),
                    'RWristYaw': (-1.8239, 1.8239)}
      
      #Variable to store the saved postures
      postures = {}
      class JointAnglesGUI:
      
          def __init__(self, master, motion_service, posture_service):
              self.master = master
              master.title("Joint Angles GUI")
              self.motion_service = motion_service
              self.posture_service = posture_service
      
              self.labels = []
              self.scalewidgets = []
      
              #TODO: Read initial angle values
              # initSensorAngles = ....
              initSensorAngles = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
              #
              print "Joints sensors readings:"
              for j, jointname in enumerate(jointsNames):
                  print (jointname, str(initSensorAngles[j]))
      
                  self.labels.append(Label(text=jointname))
                  self.labels[j].grid(row=j, column=1)
                  self.scalewidgets.append(Scale(master, from_=jointLimits[jointname][0], to=jointLimits[jointname][1], resolution=0.1, orient="horizontal", length=200))
                  self.scalewidgets[j].set(float(initSensorAngles[j]))
                  self.scalewidgets[j].bind("<ButtonRelease-1>", lambda event, widget = self.scalewidgets[j], jointname = jointsNames[j]: self.applyAngle(event, widget, jointname))
                  self.scalewidgets[j].grid(row=j, column=2)
      
              #Storing the initial position
              namePosture = 'Init'
              postures[namePosture]=initSensorAngles
      
              self.buttonreset = Button(self.master, text="Reset", command=self.callbackReset)
              self.buttonreset.grid(row=len(jointsNames)+1, column=1)
              self.buttonsave = Button(self.master, text="Save", command=self.callbackSave)
              self.buttonsave.grid(row=len(jointsNames)+1, column=2)
      
              self.labelposture = Label(text="Select Posture")
              self.labelposture.grid(row=len(jointsNames)+2,column=1)
              self.posturesmenu_var = StringVar(self.master)
              self.posturesmenu_var.set(postures.keys()[0])
              self.posturesmenu = OptionMenu(self.master, self.posturesmenu_var, postures.keys()[0], command = self.gotoPostureSelected)
              self.posturesmenu.configure(width=20)
              self.posturesmenu.grid(row=len(jointsNames)+2,column=2)
      
          def applyAngle(self, event, widget, jointname):
              print "setting", jointname, "to: ", widget.get()
              angle = widget.get()
              # TODO: complete by applying the selected angle to the robot
              # ....
              #
      
          def callbackReset(self):
              print "click!"
              # TODO: Use a ALRobotPosture to go to posture "Stand"
              # ....
              #
      
              # TODO: update value of the scalewidgets by reading the sensors
              # ....
      
          def callbackSave(self):
              print "click!"
              # TODO: Read angles of all joints
              # savedPosture = ....
              savedPosture = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
              #
              print savedPosture
              #Storing posture
              namePosture = 'Posture'+str(len(postures))
              postures[namePosture]=savedPosture
              print postures.keys()
              self.updatePosturesMenu(namePosture)
      
          def updatePosturesMenu(self, posture):
              menu=self.posturesmenu['menu']
              menu.add_command(label=posture, command=lambda v=posture: self.gotoPostureSelected(v))
      
          def gotoPostureSelected(self, posture):
              self.posturesmenu_var.set(posture)
              print self.posturesmenu_var.get()
              print "doing something"
      
              #TODO: Apply selected posture
              # ....
              #
      
      class AngleController:
          def __init__(self,master):
              self.master = master
              parser = argparse.ArgumentParser()
              parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                                  help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
              parser.add_argument("--pport", type=int, default=9559,
                                  help="Naoqi port number")
              args = parser.parse_args()
              pip = args.pip
              pport = args.pport
      
              #Starting session
              session = qi.Session()
              try:
                  session.connect("tcp://" + pip + ":" + str(pport))
              except RuntimeError:
                  print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                         "Please check your script arguments. Run with -h option for help.")
                  sys.exit(1)
      
              #Starting services
              self.motion_service  = session.service("ALMotion")
              self.posture_service = session.service("ALRobotPosture")
      
              gui = JointAnglesGUI(self.master, self.motion_service, self.posture_service)
              master.mainloop()
      
      if __name__ == "__main__":
          master = Tk()
          ac = AngleController(master)
      ```
      </details>

    * `setjointangleGUI.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      from Tkinter import *
      
      jointsNames = ["HeadYaw", "HeadPitch",
                     "LShoulderPitch", "LShoulderRoll", "LElbowYaw", "LElbowRoll", "LWristYaw",
                     "RShoulderPitch", "RShoulderRoll", "RElbowYaw", "RElbowRoll", "RWristYaw"]
      
      #joint limits taken from http://doc.aldebaran.com/2-5/family/pepper_technical/joints_pep.html
      jointLimits ={'HeadYaw': (-2.0857, 2.0857),
                    'HeadPitch': (-0.7068, 0.6371),
                    'LShoulderPitch': (-2.0857, 2.0857),
                    'LShoulderRoll': (0.0087, 1.5620),
                    'LElbowYaw': (-2.0857, 2.0857),
                    'LElbowRoll': (-1.5620, -0.0087),
                    'LWristYaw': (-1.8239, 1.8239),
                    'RShoulderPitch': (-2.0857, 2.0857),
                    'RShoulderRoll': (-1.5620, -0.0087),
                    'RElbowYaw': (-2.0857, 2.0857),
                    'RElbowRoll': (0.0087,1.5620),
                    'RWristYaw': (-1.8239, 1.8239)}
      
      class JointAnglesGUI:
      
          def __init__(self, master, motion_service):
              self.master = master
              master.title("Joint Angles GUI")
              self.motion_service = motion_service
      
              useSensors = True
              self.motion_service.setStiffnesses('Head', 1.0)
              self.motion_service.setStiffnesses('Body', 1.0)
      
              self.labels = []
              self.scalewidgets = []
              for j, jointname in enumerate(jointsNames):
                  print (j, jointname)
      
                  currentSensorAngle = self.motion_service.getAngles(jointname, useSensors)
                  print str(currentSensorAngle)
      
                  self.labels.append(Label(text=jointname))
                  self.labels[j].grid(row=j, column=1)
                  self.scalewidgets.append(Scale(master, from_=jointLimits[jointname][0], to=jointLimits[jointname][1], resolution=0.1, orient="horizontal", length=200))
                  self.scalewidgets[j].set(float(currentSensorAngle[0]))
                  self.scalewidgets[j].bind("<ButtonRelease-1>", lambda event, widget = self.scalewidgets[j], jointname = jointsNames[j]: self.updateValue(event, widget, jointname))
                  self.scalewidgets[j].grid(row=j, column=2)
      
          def updateValue(self, event, widget, jointname):
              print "setting", jointname, "to: ", widget.get()
              angles = widget.get()
              fractionMaxSpeed = 0.1
              self.motion_service.setAngles(jointname,angles,fractionMaxSpeed)
              time.sleep(0.5)
      
      #from naoqi import ALBroker
      #from naoqi import ALProxy
      import qi
      import argparse
      import sys
      import time
      import os
      
      class AngleController:
          def __init__(self,master):
              self.master = master
              parser = argparse.ArgumentParser()
              parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                                  help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
              parser.add_argument("--pport", type=int, default=9559,
                                  help="Naoqi port number")
              args = parser.parse_args()
              pip = args.pip
              pport = args.pport
      
              session = qi.Session()
              try:
                  session.connect("tcp://" + pip + ":" + str(pport))
              except RuntimeError:
                  print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                         "Please check your script arguments. Run with -h option for help.")
                  sys.exit(1)
      
              self.motion_service  = session.service("ALMotion")
      
              gui = JointAnglesGUI(self.master, self.motion_service)
              master.mainloop()
      
      if __name__ == "__main__":
          master = Tk()
          ac = AngleController(master)
      ```
      </details>

    * `stiffness.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      import qi
      import argparse
      import sys
      import time
      import os
      
      jointsNames = ["HeadYaw", "HeadPitch",
                     "LShoulderPitch", "LShoulderRoll", "LElbowYaw", "LElbowRoll", "LWristYaw",
                     "RShoulderPitch", "RShoulderRoll", "RElbowYaw", "RElbowRoll", "RWristYaw"]
      
      #joint limits taken from http://doc.aldebaran.com/2-5/family/pepper_technical/joints_pep.html
      jointLimits ={'HeadYaw': (-2.0857, 2.0857),
                    'HeadPitch': (-0.7068, 0.6371),
                    'LShoulderPitch': (-2.0857, 2.0857),
                    'LShoulderRoll': (0.0087, 1.5620),
                    'LElbowYaw': (-2.0857, 2.0857),
                    'LElbowRoll': (-1.5620, -0.0087),
                    'LWristYaw': (-1.8239, 1.8239),
                    'RShoulderPitch': (-2.0857, 2.0857),
                    'RShoulderRoll': (-1.5620, -0.0087),
                    'RElbowYaw': (-2.0857, 2.0857),
                    'RElbowRoll': (0.0087,1.5620),
                    'RWristYaw': (-1.8239, 1.8239)}
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--value", type=float, default=0.8,
                              help="Stiffness value")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          sval = args.value
      
          if (sval==None):
              print 'No stiffness value'
              sys.exit(0)
      
          #Starting session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          print "Set stiffness value: ", sval
          isAbsolute = True
      
          #Starting services
          motion_service  = session.service("ALMotion")
      
          names = ["Head", "LArm", "RArm"]
          stiffnessLists = sval
          timeLists = 1.0
          motion_service.stiffnessInterpolation(names, stiffnessLists, timeLists)
      
          time.sleep(3)
      
          print motion_service.getSummary()
      
          #print motion_service.getSummary()
      
          #names = "Body"
          #stiffnessLists = 1.0
          #timeLists = 1.0
          #motion_service.stiffnessInterpolation(names, stiffnessLists, timeLists)
      
          #time.sleep(3)
      
          #print motion_service.getSummary()
      
          #motion_service.angleInterpolation(jointsNames, jointValues, 3.0, isAbsolute)
      
      if __name__ == "__main__":
      
          main()
      
      ```
      </details>

  * **setposture/**
    * `setposture.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      
      
      import qi
      import argparse
      import sys
      import os
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--posture", type=str, default="Stand",
                              help="Desired robot posture. Choose among: Stand, StandZero, Crouch")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          posture = args.posture
      
          #Start working session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          tts_service = session.service("ALTextToSpeech")
          rp_service = session.service("ALRobotPosture")
          motion_service = session.service("ALMotion")
      
          current_posture = rp_service.getPosture()
          print "Robot posture: ", current_posture
      
          phraseToSay = "Hello! My current posture is " + current_posture
          tts_service.say(phraseToSay)
      
          if (current_posture != posture):
              phraseToSay = "Changing my posture to " + posture
              tts_service.say(phraseToSay)
      
              rp_service.goToPosture(posture,1.0)
              current_posture = rp_service.getPosture()
              print "Robot posture: ", current_posture
          else:
              phraseToSay = "Nothing to change here."
              tts_service.say(phraseToSay)
      
          if motion_service.robotIsWakeUp():
              stiff_body = 0.8
              stiff_head = 1.0
              stiff_arms = 0.9
              print "   Stiffness - Body ",stiff_body," Head ",stiff_head," Arms ",stiff_arms
      
              # Valid names: Head, LArm, RArm, LHand, RHand
      
              names = "Body"
              stiffnessLists = stiff_body
              timeLists = 1.0
              motion_service.stiffnessInterpolation(names, stiffnessLists, timeLists)
      
              names = "Head"
              stiffnessLists = stiff_head
              timeLists = 1.0
              motion_service.stiffnessInterpolation(names, stiffnessLists, timeLists)
      
              names = "LArm"
              stiffnessLists = stiff_arms
              timeLists = 1.0
              motion_service.stiffnessInterpolation(names, stiffnessLists, timeLists)
      
              names = "RArm"
              stiffnessLists = stiff_arms
              timeLists = 1.0
              motion_service.stiffnessInterpolation(names, stiffnessLists, timeLists)
      
      if __name__ == "__main__":
          main()
      ```
      </details>

  * **setstate/**
    * `setstate.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      
      
      import qi
      import argparse
      import os
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--state", type=str, default="solitary",
                              help="Desired robot state. Choose among: disabled, solitary, interactive, safeguard")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          state = args.state
      
          #Start working session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          tts_service = session.service("ALTextToSpeech")
          al_service = session.service("ALAutonomousLife")
      
          current_state = al_service.getState()
          print "Robot state: ", current_state
      
          phraseToSay = "Hello! My current state is " + current_state
          tts_service.say(phraseToSay)
      
          if (current_state != state):
              phraseToSay = "Changing my state to " + state
              tts_service.say(phraseToSay)
      
              al_service.setState(state)
              current_state = al_service.getState()
              print "Robot state: ", current_state
          else:
              phraseToSay = "Nothing to change here."
              tts_service.say(phraseToSay)
      
      if __name__ == "__main__":
          main()
      ```
      </details>

  * **slu4p/**
    * `__init__.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown

      ```
      </details>

    * **dialogue_management/**
      * `AimlParser.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        from xml.sax.handler import ContentHandler
        from xml.sax.xmlreader import Locator
        import sys
        import xml.sax
        import xml.sax.handler
        
        class AimlParserError(Exception): pass
        
        class AimlHandler(ContentHandler):
            # The legal states of the AIML parser
            _STATE_OutsideAiml = 0
            _STATE_InsideAiml = 1
            _STATE_InsideCategory = 2
            _STATE_InsidePattern = 3
            _STATE_AfterPattern = 4
            _STATE_InsideThat = 5
            _STATE_AfterThat = 6
            _STATE_InsideTemplate = 7
            _STATE_AfterTemplate = 8
        
            def __init__(self, encoding="UTF-8"):
                self.categories = {}
                self._encoding = encoding
                self._state = self._STATE_OutsideAiml
                self._version = ""
                self._namespace = ""
                self._forwardCompatibleMode = False
                self._currentPattern = ""
                self._currentThat = ""
                self._currentTopic = ""
                self._insideTopic = False
                self._currentUnknown = ""  # the name of the current unknown element
        
                # This is set to true when a parse error occurs in a category.
                self._skipCurrentCategory = False
        
                # Counts the number of parse errors in a particular AIML document.
                # query with getNumErrors().  If 0, the document is AIML-compliant.
                self._numParseErrors = 0
        
                # TODO: select the proper validInfo table based on the version number.
                self._validInfo = self._validationInfo101
        
                # This stack of bools is used when parsing <li> elements inside
                # <condition> elements, to keep track of whether or not an
                # attribute-less "default" <li> element has been found yet.  Only
                # one default <li> is allowed in each <condition> element.  We need
                # a stack in order to correctly handle nested <condition> tags.
                self._foundDefaultLiStack = []
        
                # This stack of strings indicates what the current whitespace-handling
                # behavior should be.  Each string in the stack is either "default" or
                # "preserve".  When a new AIML element is encountered, a new string is
                # pushed onto the stack, based on the value of the element's "xml:space"
                # attribute (if absent, the top of the stack is pushed again).  When
                # ending an element, pop an object off the stack.
                self._whitespaceBehaviorStack = ["default"]
        
                self._elemStack = []
                self._locator = Locator()
                self.setDocumentLocator(self._locator)
        
            def get_num_errors(self):
                "Return the number of errors found while parsing the current document."
                return self._numParseErrors
        
            def set_encoding(self, encoding):
                """Set the text encoding to use when encoding strings read from XML.
        
                Defaults to 'UTF-8'.
        
                """
                self._encoding = encoding
        
            def _location(self):
                "Return a string describing the current location in the source file."
                line = self._locator.getLineNumber()
                column = self._locator.getColumnNumber()
                return "(line %d, column %d)" % (line, column)
        
            def _push_whitespace_behavior(self, attr):
                """Push a new string onto the whitespaceBehaviorStack.
        
                The string's value is taken from the "xml:space" attribute, if it exists
                and has a legal value ("default" or "preserve").  Otherwise, the previous
                stack element is duplicated.
        
                """
                assert len(self._whitespaceBehaviorStack) > 0, "Whitespace behavior stack should never be empty!"
                try:
                    if attr["xml:space"] == "default" or attr["xml:space"] == "preserve":
                        self._whitespaceBehaviorStack.append(attr["xml:space"])
                    else:
                        raise AimlParserError, "Invalid value for xml:space attribute " + self._location()
                except KeyError:
                    self._whitespaceBehaviorStack.append(self._whitespaceBehaviorStack[-1])
        
            def startElementNS(self, name, qname, attr):
                print "QNAME:", qname
                print "NAME:", name
                uri, elem = name
                if (elem == "bot"): print "name:", attr.getValueByQName("name"), "a'ite?"
                self.startElement(elem, attr)
                pass
        
            def startElement(self, name, attr):
                # Wrapper around _startElement, which catches errors in _startElement()
                # and keeps going.
        
                # If we're inside an unknown element, ignore everything until we're
                # out again.
                if self._currentUnknown != "":
                    return
                # If we're skipping the current category, ignore everything until
                # it's finished.
                if self._skipCurrentCategory:
                    return
        
                # process this start-element.
                try:
                    self._start_element(name, attr)
                except AimlParserError, msg:
                    # Print the error message
                    sys.stderr.write("PARSE ERROR: %s\n" % msg)
        
                    self._numParseErrors += 1  # increment error count
                    # In case of a parse error, if we're inside a category, skip it.
                    if self._state >= self._STATE_InsideCategory:
                        self._skipCurrentCategory = True
        
            def _start_element(self, name, attr):
                if name == "aiml":
                    # <aiml> tags are only legal in the OutsideAiml state
                    if self._state != self._STATE_OutsideAiml:
                        raise AimlParserError, "Unexpected <aiml> tag " + self._location()
                    self._state = self._STATE_InsideAiml
                    self._insideTopic = False
                    self._currentTopic = u""
                    try:
                        self._version = attr["version"]
                    except KeyError:
                        # This SHOULD be a syntax error, but so many AIML sets out there are missing
                        # "version" attributes that it just seems nicer to let it slide.
                        # raise AimlParserError, "Missing 'version' attribute in <aiml> tag "+self._location()
                        # print "WARNING: Missing 'version' attribute in <aiml> tag "+self._location()
                        # print "         Defaulting to version 1.0"
                        self._version = "1.0"
                    self._forwardCompatibleMode = (self._version != "1.0.1")
                    self._push_whitespace_behavior(attr)
                elif self._state == self._STATE_OutsideAiml:
                    # If we're outside of an AIML element, we ignore all tags.
                    return
                elif name == "topic":
                    # <topic> tags are only legal in the InsideAiml state, and only
                    # if we're not already inside a topic.
                    if (self._state != self._STATE_InsideAiml) or self._insideTopic:
                        raise AimlParserError, "Unexpected <topic> tag", self._location()
                    try:
                        self._currentTopic = unicode(attr['name'])
                    except KeyError:
                        raise AimlParserError, "Required \"name\" attribute missing in <topic> element " + self._location()
                    self._insideTopic = True
                elif name == "category":
                    # <category> tags are only legal in the InsideAiml state
                    if self._state != self._STATE_InsideAiml:
                        raise AimlParserError, "Unexpected <category> tag " + self._location()
                    self._state = self._STATE_InsideCategory
                    self._currentPattern = u""
                    self._currentThat = u""
                    # If we're not inside a topic, the topic is implicitly set to *
                    if not self._insideTopic: self._currentTopic = u"*"
                    self._elemStack = []
                    self._push_whitespace_behavior(attr)
                elif name == "pattern":
                    # <pattern> tags are only legal in the InsideCategory state
                    if self._state != self._STATE_InsideCategory:
                        raise AimlParserError, "Unexpected <pattern> tag " + self._location()
                    self._state = self._STATE_InsidePattern
                elif name == "that" and self._state == self._STATE_AfterPattern:
                    # <that> are legal either inside a <template> element, or
                    # inside a <category> element, between the <pattern> and the
                    # <template> elements.  This clause handles the latter case.
                    self._state = self._STATE_InsideThat
                elif name == "template":
                    # <template> tags are only legal in the AfterPattern and AfterThat
                    # states
                    if self._state not in [self._STATE_AfterPattern, self._STATE_AfterThat]:
                        raise AimlParserError, "Unexpected <template> tag " + self._location()
                    # if no <that> element was specified, it is implicitly set to *
                    if self._state == self._STATE_AfterPattern:
                        self._currentThat = u"*"
                    self._state = self._STATE_InsideTemplate
                    self._elemStack.append(['template', {}])
                    self._push_whitespace_behavior(attr)
                elif self._state == self._STATE_InsidePattern:
                    # Certain tags are allowed inside <pattern> elements.
                    if name == "bot" and attr.has_key("name") and attr["name"] == u"name":
                        # Insert a special character string that the PatternMgr will
                        # replace with the bot's name.
                        self._currentPattern += u" BOT_NAME "
                    else:
                        raise AimlParserError, ("Unexpected <%s> tag " % name) + self._location()
                elif self._state == self._STATE_InsideThat:
                    # Certain tags are allowed inside <that> elements.
                    if name == "bot" and attr.has_key("name") and attr["name"] == u"name":
                        # Insert a special character string that the PatternMgr will
                        # replace with the bot's name.
                        self._currentThat += u" BOT_NAME "
                    else:
                        raise AimlParserError, ("Unexpected <%s> tag " % name) + self._location()
                elif self._state == self._STATE_InsideTemplate and self._validInfo.has_key(name):
                    # Starting a new element inside the current pattern. First
                    # we need to convert 'attr' into a native Python dictionary,
                    # so it can later be marshaled.
                    attrDict = {}
                    for k, v in attr.items():
                        # attrDict[k[1].encode(self._encoding)] = v.encode(self._encoding)
                        attrDict[k.encode(self._encoding)] = unicode(v)
                    self._validateElemStart(name, attrDict, self._version)
                    # Push the current element onto the element stack.
                    self._elemStack.append([name.encode(self._encoding), attrDict])
                    self._push_whitespace_behavior(attr)
                    # If this is a condition element, push a new entry onto the
                    # foundDefaultLiStack
                    if name == "condition":
                        self._foundDefaultLiStack.append(False)
                else:
                    # we're now inside an unknown element.
                    if self._forwardCompatibleMode:
                        # In Forward Compatibility Mode, we ignore the element and its
                        # contents.
                        self._currentUnknown = name
                    else:
                        # Otherwise, unknown elements are grounds for error!
                        raise AimlParserError, ("Unexpected <%s> tag " % name) + self._location()
        
            def characters(self, ch):
                # Wrapper around _characters which catches errors in _characters()
                # and keeps going.
                if self._state == self._STATE_OutsideAiml:
                    # If we're outside of an AIML element, we ignore all text
                    return
                if self._currentUnknown != "":
                    # If we're inside an unknown element, ignore all text
                    return
                if self._skipCurrentCategory:
                    # If we're skipping the current category, ignore all text.
                    return
                try:
                    self._characters(ch)
                except AimlParserError, msg:
                    # Print the message
                    sys.stderr.write("PARSE ERROR: %s\n" % msg)
                    self._numParseErrors += 1  # increment error count
                    # In case of a parse error, if we're inside a category, skip it.
                    if self._state >= self._STATE_InsideCategory:
                        self._skipCurrentCategory = True
        
            def _characters(self, ch):
                text = unicode(ch)
                if self._state == self._STATE_InsidePattern:
                    # TODO: text inside patterns must be upper-case!
                    self._currentPattern += text
                elif self._state == self._STATE_InsideThat:
                    self._currentThat += text
                elif self._state == self._STATE_InsideTemplate:
                    # First, see whether the element at the top of the element stack
                    # is permitted to contain text.
                    try:
                        parent = self._elemStack[-1][0]
                        parentAttr = self._elemStack[-1][1]
                        required, optional, canBeParent = self._validInfo[parent]
                        nonBlockStyleCondition = (
                            parent == "condition" and not (parentAttr.has_key("name") and parentAttr.has_key("value")))
                        if not canBeParent:
                            raise AimlParserError, ("Unexpected text inside <%s> element " % parent) + self._location()
                        elif parent == "random" or nonBlockStyleCondition:
                            # <random> elements can only contain <li> subelements. However,
                            # there's invariably some whitespace around the <li> that we need
                            # to ignore. Same for non-block-style <condition> elements (i.e.
                            # those which don't have both a "name" and a "value" attribute).
                            if len(text.strip()) == 0:
                                # ignore whitespace inside these elements.
                                return
                            else:
                                # non-whitespace text inside these elements is a syntax error.
                                raise AimlParserError, ("Unexpected text inside <%s> element " % parent) + self._location()
                    except IndexError:
                        # the element stack is empty. This should never happen.
                        raise AimlParserError, "Element stack is empty while validating text " + self._location()
        
                    # Add a new text element to the element at the top of the element
                    # stack. If there's already a text element there, simply append the
                    # new characters to its contents.
                    try:
                        textElemOnStack = (self._elemStack[-1][-1][0] == "text")
                    except IndexError:
                        textElemOnStack = False
                    except KeyError:
                        textElemOnStack = False
                    if textElemOnStack:
                        self._elemStack[-1][-1][2] += text
                    else:
                        self._elemStack[-1].append(["text", {"xml:space": self._whitespaceBehaviorStack[-1]}, text])
                else:
                    # all other text is ignored
                    pass
        
            def endElementNS(self, name, qname):
                uri, elem = name
                self.endElement(elem)
        
            def endElement(self, name):
                """Wrapper around _endElement which catches errors in _characters()
                and keeps going.
        
                """
                if self._state == self._STATE_OutsideAiml:
                    # If we're outside of an AIML element, ignore all tags
                    return
                if self._currentUnknown != "":
                    # see if we're at the end of an unknown element.  If so, we can
                    # stop ignoring everything.
                    if name == self._currentUnknown:
                        self._currentUnknown = ""
                    return
                if self._skipCurrentCategory:
                    # If we're skipping the current category, see if it's ending. We
                    # stop on ANY </category> tag, since we're not keeping track of
                    # state in ignore-mode.
                    if name == "category":
                        self._skipCurrentCategory = False
                        self._state = self._STATE_InsideAiml
                    return
                try:
                    self._endElement(name)
                except AimlParserError, msg:
                    # Print the message
                    sys.stderr.write("PARSE ERROR: %s\n" % msg)
                    self._numParseErrors += 1  # increment error count
                    # In case of a parse error, if we're inside a category, skip it.
                    if self._state >= self._STATE_InsideCategory:
                        self._skipCurrentCategory = True
        
            def _endElement(self, name):
                """Verify that an AIML end element is valid in the current
                context.
        
                Raises an AimlParserError if an illegal end element is encountered.
        
                """
                if name == "aiml":
                    # </aiml> tags are only legal in the InsideAiml state
                    if self._state != self._STATE_InsideAiml:
                        raise AimlParserError, "Unexpected </aiml> tag " + self._location()
                    self._state = self._STATE_OutsideAiml
                    self._whitespaceBehaviorStack.pop()
                elif name == "topic":
                    # </topic> tags are only legal in the InsideAiml state, and
                    # only if _insideTopic is true.
                    if self._state != self._STATE_InsideAiml or not self._insideTopic:
                        raise AimlParserError, "Unexpected </topic> tag " + self._location()
                    self._insideTopic = False
                    self._currentTopic = u""
                elif name == "category":
                    # </category> tags are only legal in the AfterTemplate state
                    if self._state != self._STATE_AfterTemplate:
                        raise AimlParserError, "Unexpected </category> tag " + self._location()
                    self._state = self._STATE_InsideAiml
                    # End the current category.  Store the current pattern/that/topic and
                    # element in the categories dictionary.
                    key = (self._currentPattern.strip(), self._currentThat.strip(), self._currentTopic.strip())
                    self.categories[key] = self._elemStack[-1]
                    self._whitespaceBehaviorStack.pop()
                elif name == "pattern":
                    # </pattern> tags are only legal in the InsidePattern state
                    if self._state != self._STATE_InsidePattern:
                        raise AimlParserError, "Unexpected </pattern> tag " + self._location()
                    self._state = self._STATE_AfterPattern
                elif name == "that" and self._state == self._STATE_InsideThat:
                    # </that> tags are only allowed inside <template> elements or in
                    # the InsideThat state.  This clause handles the latter case.
                    self._state = self._STATE_AfterThat
                elif name == "template":
                    # </template> tags are only allowed in the InsideTemplate state.
                    if self._state != self._STATE_InsideTemplate:
                        raise AimlParserError, "Unexpected </template> tag " + self._location()
                    self._state = self._STATE_AfterTemplate
                    self._whitespaceBehaviorStack.pop()
                elif self._state == self._STATE_InsidePattern:
                    # Certain tags are allowed inside <pattern> elements.
                    if name not in ["bot"]:
                        raise AimlParserError, ("Unexpected </%s> tag " % name) + self._location()
                elif self._state == self._STATE_InsideThat:
                    # Certain tags are allowed inside <that> elements.
                    if name not in ["bot"]:
                        raise AimlParserError, ("Unexpected </%s> tag " % name) + self._location()
                elif self._state == self._STATE_InsideTemplate:
                    # End of an element inside the current template.  Append the
                    # element at the top of the stack onto the one beneath it.
                    elem = self._elemStack.pop()
                    self._elemStack[-1].append(elem)
                    self._whitespaceBehaviorStack.pop()
                    # If the element was a condition, pop an item off the
                    # foundDefaultLiStack as well.
                    if elem[0] == "condition": self._foundDefaultLiStack.pop()
                else:
                    # Unexpected closing tag
                    raise AimlParserError, ("Unexpected </%s> tag " % name) + self._location()
        
            # A dictionary containing a validation information for each AIML
            # element. The keys are the names of the elements.  The values are a
            # tuple of three items. The first is a list containing the names of
            # REQUIRED attributes, the second is a list of OPTIONAL attributes,
            # and the third is a boolean value indicating whether or not the
            # element can contain other elements and/or text (if False, the
            # element can only appear in an atomic context, such as <date/>).
            _validationInfo101 = {
                "bot": (["name"], [], False),
                "condition": ([], ["name", "value"], True),  # can only contain <li> elements
                "date": ([], [], False),
                "formal": ([], [], True),
                "gender": ([], [], True),
                "get": (["name"], [], False),
                "gossip": ([], [], True),
                "id": ([], [], False),
                "input": ([], ["index"], False),
                "javascript": ([], [], True),
                "learn": ([], [], True),
                "li": ([], ["name", "value"], True),
                "lowercase": ([], [], True),
                "person": ([], [], True),
                "person2": ([], [], True),
                "random": ([], [], True),  # can only contain <li> elements
                "sentence": ([], [], True),
                "set": (["name"], [], True),
                "size": ([], [], False),
                "sr": ([], [], False),
                "srai": ([], [], True),
                "star": ([], ["index"], False),
                "system": ([], [], True),
                "template": ([], [], True),  # needs to be in the list because it can be a parent.
                "that": ([], ["index"], False),
                "thatstar": ([], ["index"], False),
                "think": ([], [], True),
                "topicstar": ([], ["index"], False),
                "uppercase": ([], [], True),
                "version": ([], [], False),
            }
        
            def _validateElemStart(self, name, attr, version):
                """Test the validity of an element starting inside a <template>
                element.
        
                This function raises an AimlParserError exception if it the tag is
                invalid.  Otherwise, no news is good news.
        
                """
                # Check the element's attributes.  Make sure that all required
                # attributes are present, and that any remaining attributes are
                # valid options.
                required, optional, canBeParent = self._validInfo[name]
                for a in required:
                    if a not in attr and not self._forwardCompatibleMode:
                        raise AimlParserError, ("Required \"%s\" attribute missing in <%s> element " % (
                            a, name)) + self._location()
                for a in attr:
                    if a in required: continue
                    if a[0:4] == "xml:": continue  # attributes in the "xml" namespace can appear anywhere
                    if a not in optional and not self._forwardCompatibleMode:
                        raise AimlParserError, ("Unexpected \"%s\" attribute in <%s> element " % (a, name)) + self._location()
        
                # special-case: several tags contain an optional "index" attribute.
                # This attribute's value must be a positive integer.
                if name in ["star", "thatstar", "topicstar"]:
                    for k, v in attr.items():
                        if k == "index":
                            temp = 0
                            try:
                                temp = int(v)
                            except:
                                raise AimlParserError, ("Bad type for \"%s\" attribute (expected integer, found \"%s\") " % (
                                    k, v)) + self._location()
                            if temp < 1:
                                raise AimlParserError, ("\"%s\" attribute must have non-negative value " % (
                                    k)) + self._location()
        
                # See whether the containing element is permitted to contain
                # subelements. If not, this element is invalid no matter what it is.
                try:
                    parent = self._elemStack[-1][0]
                    parentAttr = self._elemStack[-1][1]
                except IndexError:
                    # If the stack is empty, no parent is present.  This should never
                    # happen.
                    raise AimlParserError, ("Element stack is empty while validating <%s> " % name) + self._location()
                required, optional, canBeParent = self._validInfo[parent]
                nonBlockStyleCondition = (
                    parent == "condition" and not (parentAttr.has_key("name") and parentAttr.has_key("value")))
                if not canBeParent:
                    raise AimlParserError, ("<%s> elements cannot have any contents " % parent) + self._location()
                # Special-case test if the parent element is <condition> (the
                # non-block-style variant) or <random>: these elements can only
                # contain <li> subelements.
                elif (parent == "random" or nonBlockStyleCondition) and name != "li":
                    raise AimlParserError, ("<%s> elements can only contain <li> subelements " % parent) + self._location()
                # Special-case test for <li> elements, which can only be contained
                # by non-block-style <condition> and <random> elements, and whose
                # required attributes are dependent upon which attributes are
                # present in the <condition> parent.
                elif name == "li":
                    if not (parent == "random" or nonBlockStyleCondition):
                        raise AimlParserError, (
                                                   "Unexpected <li> element contained by <%s> element " % parent) + self._location()
                    if nonBlockStyleCondition:
                        if parentAttr.has_key("name"):
                            # Single-predicate condition.  Each <li> element except the
                            # last must have a "value" attribute.
                            if len(attr) == 0:
                                # This could be the default <li> element for this <condition>,
                                # unless we've already found one.
                                if self._foundDefaultLiStack[-1]:
                                    raise AimlParserError, "Unexpected default <li> element inside <condition> " + self._location()
                                else:
                                    self._foundDefaultLiStack[-1] = True
                            elif len(attr) == 1 and attr.has_key("value"):
                                pass  # this is the valid case
                            else:
                                raise AimlParserError, "Invalid <li> inside single-predicate <condition> " + self._location()
                        elif len(parentAttr) == 0:
                            # Multi-predicate condition.  Each <li> element except the
                            # last must have a "name" and a "value" attribute.
                            if len(attr) == 0:
                                # This could be the default <li> element for this <condition>,
                                # unless we've already found one.
                                if self._foundDefaultLiStack[-1]:
                                    raise AimlParserError, "Unexpected default <li> element inside <condition> " + self._location()
                                else:
                                    self._foundDefaultLiStack[-1] = True
                            elif len(attr) == 2 and attr.has_key("value") and attr.has_key("name"):
                                pass  # this is the valid case
                            else:
                                raise AimlParserError, "Invalid <li> inside multi-predicate <condition> " + self._location()
                # All is well!
                return True
        
        def create_parser():
            """Create and return an AIML parser object."""
            parser = xml.sax.make_parser()
            handler = AimlHandler("UTF-8")
            parser.setContentHandler(handler)
            # parser.setFeature(xml.sax.handler.feature_namespaces, True)
            return parser
        ```
        </details>

      * `DefaultSubs.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        """This file contains the default (English) substitutions for the
        PyAIML kernel.  These substitutions may be overridden by using the
        Kernel.loadSubs(filename) method.  The filename specified should refer
        to a Windows-style INI file with the following format:
        
            # lines that start with '#' are comments
        
            # The 'gender' section contains the substitutions performed by the
            # <gender> AIML tag, which swaps masculine and feminine pronouns.
            [gender]
            he = she
            she = he
            # and so on...
        
            # The 'person' section contains the substitutions performed by the
            # <person> AIML tag, which swaps 1st and 2nd person pronouns.
            [person]
            I = you
            you = I
            # and so on...
        
            # The 'person2' section contains the substitutions performed by
            # the <person2> AIML tag, which swaps 1st and 3nd person pronouns.
            [person2]
            I = he
            he = I
            # and so on...
        
            # the 'normal' section contains subtitutions run on every input
            # string passed into Kernel.respond().  It's mainly used to
            # correct common misspellings, and to convert contractions
            # ("WHAT'S") into a format that will match an AIML pattern ("WHAT
            # IS").
            [normal]
            what's = what is
        """
        
        defaultGender = {
            # masculine -> feminine
            "he": "she",
            "him": "her",
            "his": "her",
            "himself": "herself",
        
            # feminine -> masculine
            "she": "he",
            "her": "him",
            "hers": "his",
            "herself": "himself",
        }
        
        defaultPerson = {
            # 1st->3rd (masculine)
            "I": "he",
            "me": "him",
            "my": "his",
            "mine": "his",
            "myself": "himself",
        
            # 3rd->1st (masculine)
            "he":"I",
            "him":"me",
            "his":"my",
            "himself":"myself",
        
            # 3rd->1st (feminine)
            "she":"I",
            "her":"me",
            "hers":"mine",
            "herself":"myself",
        }
        
        defaultPerson2 = {
            # 1st -> 2nd
            "I": "you",
            "me": "you",
            "my": "your",
            "mine": "yours",
            "myself": "yourself",
        
            # 2nd -> 1st
            "you": "me",
            "your": "my",
            "yours": "mine",
            "yourself": "myself",
        }
        
        # TODO: this list is far from complete
        defaultNormal = {
            "wanna": "want to",
            "gonna": "going to",
        
            "I'm": "I am",
            "I'd": "I would",
            "I'll": "I will",
            "I've": "I have",
            "you'd": "you would",
            "you're": "you are",
            "you've": "you have",
            "you'll": "you will",
            "he's": "he is",
            "he'd": "he would",
            "he'll": "he will",
            "she's": "she is",
            "she'd": "she would",
            "she'll": "she will",
            "we're": "we are",
            "we'd": "we would",
            "we'll": "we will",
            "we've": "we have",
            "they're": "they are",
            "they'd": "they would",
            "they'll": "they will",
            "they've": "they have",
        
            "y'all": "you all",
        
            "can't": "can not",
            "cannot": "can not",
            "couldn't": "could not",
            "wouldn't": "would not",
            "shouldn't": "should not",
        
            "isn't": "is not",
            "ain't": "is not",
            "don't": "do not",
            "aren't": "are not",
            "won't": "will not",
            "weren't": "were not",
            "wasn't": "was not",
            "didn't": "did not",
            "hasn't": "has not",
            "hadn't": "had not",
            "haven't": "have not",
        
            "where's": "where is",
            "where'd": "where did",
            "where'll": "where will",
            "who's": "who is",
            "who'd": "who did",
            "who'll": "who will",
            "what's": "what is",
            "what'd": "what did",
            "what'll": "what will",
            "when's": "when is",
            "when'd": "when did",
            "when'll": "when will",
            "why's": "why is",
            "why'd": "why did",
            "why'll": "why will",
        
            "it's": "it is",
            "it'd": "it would",
            "it'll": "it will",
        }
        ```
        </details>

      * `Kernel.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        # -*- coding: latin-1 -*-
        """This file contains the public interface to the aiml module."""
        import AimlParser
        import DefaultSubs
        import Utils
        from PatternMgr import PatternMgr
        from WordSub import WordSub
        
        from ConfigParser import ConfigParser
        import copy
        import glob
        import os
        import random
        import re
        import string
        import sys
        import time
        import threading
        import xml.sax
        
        class Kernel:
            # module constants
            _globalSessionID = "_global"  # key of the global session (duh)
            _maxHistorySize = 10  # maximum length of the _inputs and _responses lists
            _maxRecursionDepth = 100  # maximum number of recursive <srai>/<sr> tags before the response is aborted.
            # special predicate keys
            _inputHistory = "_inputHistory"  # keys to a queue (list) of recent user input
            _outputHistory = "_outputHistory"  # keys to a queue (list) of recent responses.
            _inputStack = "_inputStack"  # Should always be empty in between calls to respond()
        
            def __init__(self):
                self._verboseMode = True
                self._version = "PyAIML 0.8.6"
                self._brain = PatternMgr()
                self._respondLock = threading.RLock()
                self._textEncoding = "utf-8"
        
                # set up the sessions
                self._sessions = {}
                self._add_session(self._globalSessionID)
        
                # Set up the bot predicates
                self._botPredicates = {}
                self.set_bot_predicate("name", "Nameless")
        
                # set up the word substitutors (subbers):
                self._subbers = {}
                self._subbers['gender'] = WordSub(DefaultSubs.defaultGender)
                self._subbers['person'] = WordSub(DefaultSubs.defaultPerson)
                self._subbers['person2'] = WordSub(DefaultSubs.defaultPerson2)
                self._subbers['normal'] = WordSub(DefaultSubs.defaultNormal)
        
                # set up the element processors
                self._elementProcessors = {
                    "bot": self._processBot,
                    "condition": self._processCondition,
                    "date": self._processDate,
                    "formal": self._processFormal,
                    "gender": self._processGender,
                    "get": self._processGet,
                    "gossip": self._processGossip,
                    "id": self._processId,
                    "input": self._processInput,
                    "javascript": self._processJavascript,
                    "learn": self._processLearn,
                    "li": self._processLi,
                    "lowercase": self._processLowercase,
                    "person": self._processPerson,
                    "person2": self._processPerson2,
                    "random": self._processRandom,
                    "text": self._processText,
                    "sentence": self._processSentence,
                    "set": self._processSet,
                    "size": self._processSize,
                    "sr": self._processSr,
                    "srai": self._processSrai,
                    "star": self._processStar,
                    "system": self._processSystem,
                    "template": self._processTemplate,
                    "that": self._processThat,
                    "thatstar": self._processThatstar,
                    "think": self._processThink,
                    "topicstar": self._processTopicstar,
                    "uppercase": self._processUppercase,
                    "version": self._processVersion,
                }
        
            def bootstrap(self, brainFile=None, learnFiles=[], commands=[]):
                """Prepare a Kernel object for use.
        
                If a brainFile argument is provided, the Kernel attempts to
                load the brain at the specified filename.
        
                If learnFiles is provided, the Kernel attempts to load the
                specified AIML files.
        
                Finally, each of the input strings in the commands list is
                passed to respond().
        
                """
                start = time.clock()
                if brainFile:
                    self.load_brain(brainFile)
        
                # learnFiles might be a string, in which case it should be
                # turned into a single-element list.
                learns = learnFiles
                try:
                    learns = [learnFiles + ""]
                except:
                    pass
                for file in learns:
                    self.learn(file)
        
                # ditto for commands
                cmds = commands
                try:
                    cmds = [commands + ""]
                except:
                    pass
                for cmd in cmds:
                    print self._respond(cmd, self._globalSessionID)
        
                if self._verboseMode:
                    print "Kernel bootstrap completed in %.2f seconds" % (time.clock() - start)
        
            def verbose(self, isVerbose=True):
                """Enable/disable verbose output mode."""
                self._verboseMode = isVerbose
        
            def version(self):
                """Return the Kernel's version string."""
                return self._version
        
            def num_categories(self):
                """Return the number of categories the Kernel has learned."""
                # there's a one-to-one mapping between templates and categories
                return self._brain.numTemplates()
        
            def reset_brain(self):
                """Reset the brain to its initial state.
        
                This is essentially equivilant to:
                    del(kern)
                    kern = aiml.Kernel()
        
                """
                del (self._brain)
                self.__init__()
        
            def load_brain(self, filename):
                """Attempt to load a previously-saved 'brain' from the
                specified filename.
        
                NOTE: the current contents of the 'brain' will be discarded!
        
                """
                if self._verboseMode: print "Loading brain from %s..." % filename,
                start = time.clock()
                self._brain.restore(filename)
                if self._verboseMode:
                    end = time.clock() - start
                    print "done (%d categories in %.2f seconds)" % (self._brain.numTemplates(), end)
        
            def save_brain(self, filename):
                """Dump the contents of the bot's brain to a file on disk."""
                if self._verboseMode: print "Saving brain to %s..." % filename,
                start = time.clock()
                self._brain.save(filename)
                if self._verboseMode:
                    print "done (%.2f seconds)" % (time.clock() - start)
        
            def get_predicate(self, name, sessionID=_globalSessionID):
                """Retrieve the current value of the predicate 'name' from the
                specified session.
        
                If name is not a valid predicate in the session, the empty
                string is returned.
        
                """
                try:
                    return self._sessions[sessionID][name]
                except KeyError:
                    return ""
        
            def set_predicate(self, name, value, sessionID=_globalSessionID):
                """Set the value of the predicate 'name' in the specified
                session.
        
                If sessionID is not a valid session, it will be created. If
                name is not a valid predicate in the session, it will be
                created.
        
                """
                self._add_session(sessionID)  # add the session, if it doesn't already exist.
                self._sessions[sessionID][name] = value
        
            def get_bot_predicate(self, name):
                """Retrieve the value of the specified bot predicate.
        
                If name is not a valid bot predicate, the empty string is returned.
        
                """
                try:
                    return self._botPredicates[name]
                except KeyError:
                    return ""
        
            def set_bot_predicate(self, name, value):
                """Set the value of the specified bot predicate.
        
                If name is not a valid bot predicate, it will be created.
        
                """
                self._botPredicates[name] = value
                # Clumsy hack: if updating the bot name, we must update the
                # name in the brain as well
                if name == "name":
                    self._brain.setBotName(self.get_bot_predicate("name"))
        
            def set_text_encoding(self, encoding):
                """Set the text encoding used when loading AIML files (Latin-1, UTF-8, etc.)."""
                self._textEncoding = encoding
        
            def load_subs(self, filename):
                """Load a substitutions file.
        
                The file must be in the Windows-style INI format (see the
                standard ConfigParser module docs for information on this
                format).  Each section of the file is loaded into its own
                substituter.
        
                """
                inFile = file(filename)
                parser = ConfigParser()
                parser.readfp(inFile, filename)
                inFile.close()
                for s in parser.sections():
                    # Add a new WordSub instance for this section.  If one already
                    # exists, delete it.
                    if self._subbers.has_key(s):
                        del (self._subbers[s])
                    self._subbers[s] = WordSub()
                    # iterate over the key,value pairs and add them to the subber
                    for k, v in parser.items(s):
                        self._subbers[s][k] = v
        
            def _add_session(self, sessionID):
                """Create a new session with the specified ID string."""
                if self._sessions.has_key(sessionID):
                    return
                # Create the session.
                self._sessions[sessionID] = {
                    # Initialize the special reserved predicates
                    self._inputHistory: [],
                    self._outputHistory: [],
                    self._inputStack: []
                }
        
            def _delete_session(self, sessionID):
                """Delete the specified session."""
                if self._sessions.has_key(sessionID):
                    self._sessions.pop(sessionID)
        
            def get_session_data(self, sessionID=None):
                """Return a copy of the session data dictionary for the
                specified session.
        
                If no sessionID is specified, return a dictionary containing
                *all* of the individual session dictionaries.
        
                """
                s = None
                if sessionID is not None:
                    try:
                        s = self._sessions[sessionID]
                    except KeyError:
                        s = {}
                else:
                    s = self._sessions
                return copy.deepcopy(s)
        
            def learn(self, filename):
                """Load and learn the contents of the specified AIML file.
        
                If filename includes wildcard characters, all matching files
                will be loaded and learned.
        
                """
                for f in glob.glob(filename):
                    if self._verboseMode: print "Loading %s..." % f,
                    start = time.clock()
                    # Load and parse the AIML file.
                    parser = AimlParser.create_parser()
                    handler = parser.getContentHandler()
                    handler.set_encoding(self._textEncoding)
                    try:
                        parser.parse(f)
                    except xml.sax.SAXParseException, msg:
                        err = "\nFATAL PARSE ERROR in file %s:\n%s\n" % (f, msg)
                        sys.stderr.write(err)
                        continue
                    # store the pattern/template pairs in the PatternMgr.
                    for key, tem in handler.categories.items():
                        self._brain.add(key, tem)
                    # Parsing was successful.
                    if self._verboseMode:
                        print "done (%.2f seconds)" % (time.clock() - start)
        
            def respond(self, input, sessionID=_globalSessionID):
                """Return the Kernel's response to the input string."""
                if len(input) == 0:
                    return ""
        
                # ensure that input is a unicode string
                try:
                    input = input.decode(self._textEncoding, 'replace')
                except UnicodeError:
                    pass
                except AttributeError:
                    pass
        
                # prevent other threads from stomping all over us.
                self._respondLock.acquire()
        
                # Add the session, if it doesn't already exist
                self._add_session(sessionID)
        
                # split the input into discrete sentences
                sentences = Utils.sentences(input)
                finalResponse = ""
                for s in sentences:
                    # Add the input to the history list before fetching the
                    # response, so that <input/> tags work properly.
                    inputHistory = self.get_predicate(self._inputHistory, sessionID)
                    inputHistory.append(s)
                    while len(inputHistory) > self._maxHistorySize:
                        inputHistory.pop(0)
                    self.set_predicate(self._inputHistory, inputHistory, sessionID)
        
                    # Fetch the response
                    response = self._respond(s, sessionID)
        
                    # add the data from this exchange to the history lists
                    outputHistory = self.get_predicate(self._outputHistory, sessionID)
                    outputHistory.append(response)
                    while len(outputHistory) > self._maxHistorySize:
                        outputHistory.pop(0)
                    self.set_predicate(self._outputHistory, outputHistory, sessionID)
        
                    # append this response to the final response.
                    finalResponse += (response + "  ")
                finalResponse = finalResponse.strip()
        
                assert (len(self.get_predicate(self._inputStack, sessionID)) == 0)
        
                # release the lock and return
                self._respondLock.release()
                try:
                    return finalResponse.encode(self._textEncoding)
                except UnicodeError:
                    return finalResponse
        
            # This version of _respond() just fetches the response for some input.
            # It does not mess with the input and output histories.  Recursive calls
            # to respond() spawned from tags like <srai> should call this function
            # instead of respond().
            def _respond(self, input, sessionID):
                """Private version of respond(), does the real work."""
                if len(input) == 0:
                    return ""
        
                # guard against infinite recursion
                inputStack = self.get_predicate(self._inputStack, sessionID)
                if len(inputStack) > self._maxRecursionDepth:
                    if self._verboseMode:
                        err = "WARNING: maximum recursion depth exceeded (input='%s')" % input.encode(self._textEncoding,
                                                                                                      'replace')
                        sys.stderr.write(err)
                    return ""
        
                # push the input onto the input stack
                inputStack = self.get_predicate(self._inputStack, sessionID)
                inputStack.append(input)
                self.set_predicate(self._inputStack, inputStack, sessionID)
        
                # run the input through the 'normal' subber
                subbedInput = self._subbers['normal'].sub(input)
        
                # fetch the bot's previous response, to pass to the match()
                # function as 'that'.
                outputHistory = self.get_predicate(self._outputHistory, sessionID)
                try:
                    that = outputHistory[-1]
                except IndexError:
                    that = ""
                subbedThat = self._subbers['normal'].sub(that)
        
                # fetch the current topic
                topic = self.get_predicate("topic", sessionID)
                subbedTopic = self._subbers['normal'].sub(topic)
        
                # Determine the final response.
                response = ""
                elem = self._brain.match(subbedInput, subbedThat, subbedTopic)
                if elem is None:
                    if self._verboseMode:
                        err = "WARNING: No match found for input: %s\n" % input.encode(self._textEncoding)
                        sys.stderr.write(err)
                else:
                    # Process the element into a response string.
                    response += self._processElement(elem, sessionID).strip()
                    response += " "
                response = response.strip()
        
                # pop the top entry off the input stack.
                inputStack = self.get_predicate(self._inputStack, sessionID)
                inputStack.pop()
                self.set_predicate(self._inputStack, inputStack, sessionID)
        
                return response
        
            def _processElement(self, elem, sessionID):
                """Process an AIML element.
        
                The first item of the elem list is the name of the element's
                XML tag.  The second item is a dictionary containing any
                attributes passed to that tag, and their values.  Any further
                items in the list are the elements enclosed by the current
                element's begin and end tags; they are handled by each
                element's handler function.
        
                """
                try:
                    handlerFunc = self._elementProcessors[elem[0]]
                except:
                    # Oops -- there's no handler function for this element
                    # type!
                    if self._verboseMode:
                        err = "WARNING: No handler found for <%s> element\n" % elem[0].encode(self._textEncoding, 'replace')
                        sys.stderr.write(err)
                    return ""
                return handlerFunc(elem, sessionID)
        
            ######################################################
            ### Individual element-processing functions follow ###
            ######################################################
        
            # <bot>
            def _processBot(self, elem, sessionID):
                """Process a <bot> AIML element.
        
                Required element attributes:
                    name: The name of the bot predicate to retrieve.
        
                <bot> elements are used to fetch the value of global,
                read-only "bot predicates."  These predicates cannot be set
                from within AIML; you must use the setBotPredicate() function.
        
                """
                attrName = elem[1]['name']
                return self.get_bot_predicate(attrName)
        
            # <condition>
            def _processCondition(self, elem, sessionID):
                """Process a <condition> AIML element.
        
                Optional element attributes:
                    name: The name of a predicate to test.
                    value: The value to test the predicate for.
        
                <condition> elements come in three flavors.  Each has different
                attributes, and each handles their contents differently.
        
                The simplest case is when the <condition> tag has both a 'name'
                and a 'value' attribute.  In this case, if the predicate
                'name' has the value 'value', then the contents of the element
                are processed and returned.
        
                If the <condition> element has only a 'name' attribute, then
                its contents are a series of <li> elements, each of which has
                a 'value' attribute.  The list is scanned from top to bottom
                until a match is found.  Optionally, the last <li> element can
                have no 'value' attribute, in which case it is processed and
                returned if no other match is found.
        
                If the <condition> element has neither a 'name' nor a 'value'
                attribute, then it behaves almost exactly like the previous
                case, except that each <li> subelement (except the optional
                last entry) must now include both 'name' and 'value'
                attributes.
        
                """
                attr = None
                response = ""
                attr = elem[1]
        
                # Case #1: test the value of a specific predicate for a
                # specific value.
                if attr.has_key('name') and attr.has_key('value'):
                    val = self.get_predicate(attr['name'], sessionID)
                    if val == attr['value']:
                        for e in elem[2:]:
                            response += self._processElement(e, sessionID)
                        return response
                else:
                    # Case #2 and #3: Cycle through <li> contents, testing a
                    # name and value pair for each one.
                    try:
                        name = None
                        if attr.has_key('name'):
                            name = attr['name']
                        # Get the list of <li> elemnents
                        listitems = []
                        for e in elem[2:]:
                            if e[0] == 'li':
                                listitems.append(e)
                        # if listitems is empty, return the empty string
                        if len(listitems) == 0:
                            return ""
                        # iterate through the list looking for a condition that
                        # matches.
                        foundMatch = False
                        for li in listitems:
                            try:
                                liAttr = li[1]
                                # if this is the last list item, it's allowed
                                # to have no attributes.  We just skip it for now.
                                if len(liAttr.keys()) == 0 and li == listitems[-1]:
                                    continue
                                # get the name of the predicate to test
                                liName = name
                                if liName == None:
                                    liName = liAttr['name']
                                # get the value to check against
                                liValue = liAttr['value']
                                # do the test
                                if self.get_predicate(liName, sessionID) == liValue:
                                    foundMatch = True
                                    response += self._processElement(li, sessionID)
                                    break
                            except:
                                # No attributes, no name/value attributes, no
                                # such predicate/session, or processing error.
                                if self._verboseMode: print "Something amiss -- skipping listitem", li
                                raise
                        if not foundMatch:
                            # Check the last element of listitems.  If it has
                            # no 'name' or 'value' attribute, process it.
                            try:
                                li = listitems[-1]
                                liAttr = li[1]
                                if not (liAttr.has_key('name') or liAttr.has_key('value')):
                                    response += self._processElement(li, sessionID)
                            except:
                                # listitems was empty, no attributes, missing
                                # name/value attributes, or processing error.
                                if self._verboseMode: print "error in default listitem"
                                raise
                    except:
                        # Some other catastrophic cataclysm
                        if self._verboseMode: print "catastrophic condition failure"
                        raise
                return response
        
            # <date>
            def _processDate(self, elem, sessionID):
                """Process a <date> AIML element.
        
                <date> elements resolve to the current date and time.  The
                AIML specification doesn't require any particular format for
                this information, so I go with whatever's simplest.
        
                """
                return time.asctime()
        
            # <formal>
            def _processFormal(self, elem, sessionID):
                """Process a <formal> AIML element.
        
                <formal> elements process their contents recursively, and then
                capitalize the first letter of each word of the result.
        
                """
                response = ""
                for e in elem[2:]:
                    response += self._processElement(e, sessionID)
                return string.capwords(response)
        
            # <gender>
            def _processGender(self, elem, sessionID):
                """Process a <gender> AIML element.
        
                <gender> elements process their contents, and then swap the
                gender of any third-person singular pronouns in the result.
                This subsitution is handled by the aiml.WordSub module.
        
                """
                response = ""
                for e in elem[2:]:
                    response += self._processElement(e, sessionID)
                return self._subbers['gender'].sub(response)
        
            # <get>
            def _processGet(self, elem, sessionID):
                """Process a <get> AIML element.
        
                Required element attributes:
                    name: The name of the predicate whose value should be
                    retrieved from the specified session and returned.  If the
                    predicate doesn't exist, the empty string is returned.
        
                <get> elements return the value of a predicate from the
                specified session.
        
                """
                return self.get_predicate(elem[1]['name'], sessionID)
        
            # <gossip>
            def _processGossip(self, elem, sessionID):
                """Process a <gossip> AIML element.
        
                <gossip> elements are used to capture and store user input in
                an implementation-defined manner, theoretically allowing the
                bot to learn from the people it chats with.  I haven't
                descided how to define my implementation, so right now
                <gossip> behaves identically to <think>.
        
                """
                return self._processThink(elem, sessionID)
        
            # <id>
            def _processId(self, elem, sessionID):
                """ Process an <id> AIML element.
        
                <id> elements return a unique "user id" for a specific
                conversation.  In PyAIML, the user id is the name of the
                current session.
        
                """
                return sessionID
        
            # <input>
            def _processInput(self, elem, sessionID):
                """Process an <input> AIML element.
        
                Optional attribute elements:
                    index: The index of the element from the history list to
                    return. 1 means the most recent item, 2 means the one
                    before that, and so on.
        
                <input> elements return an entry from the input history for
                the current session.
        
                """
                inputHistory = self.get_predicate(self._inputHistory, sessionID)
                try:
                    index = int(elem[1]['index'])
                except:
                    index = 1
                try:
                    return inputHistory[-index]
                except IndexError:
                    if self._verboseMode:
                        err = "No such index %d while processing <input> element.\n" % index
                        sys.stderr.write(err)
                    return ""
        
            # <javascript>
            def _processJavascript(self, elem, sessionID):
                """Process a <javascript> AIML element.
        
                <javascript> elements process their contents recursively, and
                then run the results through a server-side Javascript
                interpreter to compute the final response.  Implementations
                are not required to provide an actual Javascript interpreter,
                and right now PyAIML doesn't; <javascript> elements are behave
                exactly like <think> elements.
        
                """
                return self._processThink(elem, sessionID)
        
            # <learn>
            def _processLearn(self, elem, sessionID):
                """Process a <learn> AIML element.
        
                <learn> elements process their contents recursively, and then
                treat the result as an AIML file to open and learn.
        
                """
                filename = ""
                for e in elem[2:]:
                    filename += self._processElement(e, sessionID)
                self.learn(filename)
                return ""
        
            # <li>
            def _processLi(self, elem, sessionID):
                """Process an <li> AIML element.
        
                Optional attribute elements:
                    name: the name of a predicate to query.
                    value: the value to check that predicate for.
        
                <li> elements process their contents recursively and return
                the results. They can only appear inside <condition> and
                <random> elements.  See _processCondition() and
                _processRandom() for details of their usage.
        
                """
                response = ""
                for e in elem[2:]:
                    response += self._processElement(e, sessionID)
                return response
        
            # <lowercase>
            def _processLowercase(self, elem, sessionID):
                """Process a <lowercase> AIML element.
        
                <lowercase> elements process their contents recursively, and
                then convert the results to all-lowercase.
        
                """
                response = ""
                for e in elem[2:]:
                    response += self._processElement(e, sessionID)
                return string.lower(response)
        
            # <person>
            def _processPerson(self, elem, sessionID):
                """Process a <person> AIML element.
        
                <person> elements process their contents recursively, and then
                convert all pronouns in the results from 1st person to 2nd
                person, and vice versa.  This subsitution is handled by the
                aiml.WordSub module.
        
                If the <person> tag is used atomically (e.g. <person/>), it is
                a shortcut for <person><star/></person>.
        
                """
                response = ""
                for e in elem[2:]:
                    response += self._processElement(e, sessionID)
                if len(elem[2:]) == 0:  # atomic <person/> = <person><star/></person>
                    response = self._processElement(['star', {}], sessionID)
                return self._subbers['person'].sub(response)
        
            # <person2>
            def _processPerson2(self, elem, sessionID):
                """Process a <person2> AIML element.
        
                <person2> elements process their contents recursively, and then
                convert all pronouns in the results from 1st person to 3rd
                person, and vice versa.  This subsitution is handled by the
                aiml.WordSub module.
        
                If the <person2> tag is used atomically (e.g. <person2/>), it is
                a shortcut for <person2><star/></person2>.
        
                """
                response = ""
                for e in elem[2:]:
                    response += self._processElement(e, sessionID)
                if len(elem[2:]) == 0:  # atomic <person2/> = <person2><star/></person2>
                    response = self._processElement(['star', {}], sessionID)
                return self._subbers['person2'].sub(response)
        
            # <random>
            def _processRandom(self, elem, sessionID):
                """Process a <random> AIML element.
        
                <random> elements contain zero or more <li> elements.  If
                none, the empty string is returned.  If one or more <li>
                elements are present, one of them is selected randomly to be
                processed recursively and have its results returned.  Only the
                chosen <li> element's contents are processed.  Any non-<li> contents are
                ignored.
        
                """
                listitems = []
                for e in elem[2:]:
                    if e[0] == 'li':
                        listitems.append(e)
                if len(listitems) == 0:
                    return ""
        
                # select and process a random listitem.
                random.shuffle(listitems)
                return self._processElement(listitems[0], sessionID)
        
            # <sentence>
            def _processSentence(self, elem, sessionID):
                """Process a <sentence> AIML element.
        
                <sentence> elements process their contents recursively, and
                then capitalize the first letter of the results.
        
                """
                response = ""
                for e in elem[2:]:
                    response += self._processElement(e, sessionID)
                try:
                    response = response.strip()
                    words = string.split(response, " ", 1)
                    words[0] = string.capitalize(words[0])
                    response = string.join(words)
                    return response
                except IndexError:  # response was empty
                    return ""
        
            # <set>
            def _processSet(self, elem, sessionID):
                """Process a <set> AIML element.
        
                Required element attributes:
                    name: The name of the predicate to set.
        
                <set> elements process their contents recursively, and assign the results to a predicate
                (given by their 'name' attribute) in the current session.  The contents of the element
                are also returned.
        
                """
                value = ""
                for e in elem[2:]:
                    value += self._processElement(e, sessionID)
                self.set_predicate(elem[1]['name'], value, sessionID)
                return value
        
            # <size>
            def _processSize(self, elem, sessionID):
                """Process a <size> AIML element.
        
                <size> elements return the number of AIML categories currently
                in the bot's brain.
        
                """
                return str(self.num_categories())
        
            # <sr>
            def _processSr(self, elem, sessionID):
                """Process an <sr> AIML element.
        
                <sr> elements are shortcuts for <srai><star/></srai>.
        
                """
                star = self._processElement(['star', {}], sessionID)
                response = self._respond(star, sessionID)
                return response
        
            # <srai>
            def _processSrai(self, elem, sessionID):
                """Process a <srai> AIML element.
        
                <srai> elements recursively process their contents, and then
                pass the results right back into the AIML interpreter as a new
                piece of input.  The results of this new input string are
                returned.
        
                """
                newInput = ""
                for e in elem[2:]:
                    newInput += self._processElement(e, sessionID)
                return self._respond(newInput, sessionID)
        
            # <star>
            def _processStar(self, elem, sessionID):
                """Process a <star> AIML element.
        
                Optional attribute elements:
                    index: Which "*" character in the current pattern should
                    be matched?
        
                <star> elements return the text fragment matched by the "*"
                character in the current input pattern.  For example, if the
                input "Hello Tom Smith, how are you?" matched the pattern
                "HELLO * HOW ARE YOU", then a <star> element in the template
                would evaluate to "Tom Smith".
        
                """
                try:
                    index = int(elem[1]['index'])
                except KeyError:
                    index = 1
                # fetch the user's last input
                inputStack = self.get_predicate(self._inputStack, sessionID)
                input = self._subbers['normal'].sub(inputStack[-1])
                # fetch the Kernel's last response (for 'that' context)
                outputHistory = self.get_predicate(self._outputHistory, sessionID)
                try:
                    that = self._subbers['normal'].sub(outputHistory[-1])
                except:
                    that = ""  # there might not be any output yet
                topic = self.get_predicate("topic", sessionID)
                response = self._brain.star("star", input, that, topic, index)
                return response
        
            # <system>
            def _processSystem(self, elem, sessionID):
                """Process a <system> AIML element.
        
                <system> elements process their contents recursively, and then
                attempt to execute the results as a shell command on the
                server.  The AIML interpreter blocks until the command is
                complete, and then returns the command's output.
        
                For cross-platform compatibility, any file paths inside
                <system> tags should use Unix-style forward slashes ("/") as a
                directory separator.
        
                """
                # build up the command string
                command = ""
                for e in elem[2:]:
                    command += self._processElement(e, sessionID)
        
                # normalize the path to the command.  Under Windows, this
                # switches forward-slashes to back-slashes; all system
                # elements should use unix-style paths for cross-platform
                # compatibility.
                # executable,args = command.split(" ", 1)
                # executable = os.path.normpath(executable)
                # command = executable + " " + args
                command = os.path.normpath(command)
        
                # execute the command.
                response = ""
                try:
                    out = os.popen(command)
                except RuntimeError, msg:
                    if self._verboseMode:
                        err = "WARNING: RuntimeError while processing \"system\" element:\n%s\n" % msg.encode(
                            self._textEncoding, 'replace')
                        sys.stderr.write(err)
                    return "There was an error while computing my response.  Please inform my botmaster."
                time.sleep(0.01)  # I'm told this works around a potential IOError exception.
                for line in out:
                    response += line + "\n"
                response = string.join(response.splitlines()).strip()
                return response
        
            # <template>
            def _processTemplate(self, elem, sessionID):
                """Process a <template> AIML element.
        
                <template> elements recursively process their contents, and
                return the results.  <template> is the root node of any AIML
                response tree.
        
                """
                response = ""
                for e in elem[2:]:
                    response += self._processElement(e, sessionID)
                return response
        
            # text
            def _processText(self, elem, sessionID):
                """Process a raw text element.
        
                Raw text elements aren't really AIML tags. Text elements cannot contain
                other elements; instead, the third item of the 'elem' list is a text
                string, which is immediately returned. They have a single attribute,
                automatically inserted by the parser, which indicates whether whitespace
                in the text should be preserved or not.
        
                """
                try:
                    elem[2] + ""
                except TypeError:
                    raise TypeError, "Text element contents are not text"
        
                # If the the whitespace behavior for this element is "default",
                # we reduce all stretches of >1 whitespace characters to a single
                # space.  To improve performance, we do this only once for each
                # text element encountered, and save the results for the future.
                if elem[1]["xml:space"] == "default":
                    elem[2] = re.sub("\s+", " ", elem[2])
                    elem[1]["xml:space"] = "preserve"
                return elem[2]
        
            # <that>
            def _processThat(self, elem, sessionID):
                """Process a <that> AIML element.
        
                Optional element attributes:
                    index: Specifies which element from the output history to
                    return.  1 is the most recent response, 2 is the next most
                    recent, and so on.
        
                <that> elements (when they appear inside <template> elements)
                are the output equivilant of <input> elements; they return one
                of the Kernel's previous responses.
        
                """
                outputHistory = self.get_predicate(self._outputHistory, sessionID)
                index = 1
                try:
                    # According to the AIML spec, the optional index attribute
                    # can either have the form "x" or "x,y". x refers to how
                    # far back in the output history to go.  y refers to which
                    # sentence of the specified response to return.
                    index = int(elem[1]['index'].split(',')[0])
                except:
                    pass
                try:
                    return outputHistory[-index]
                except IndexError:
                    if self._verboseMode:
                        err = "No such index %d while processing <that> element.\n" % index
                        sys.stderr.write(err)
                    return ""
        
            # <thatstar>
            def _processThatstar(self, elem, sessionID):
                """Process a <thatstar> AIML element.
        
                Optional element attributes:
                    index: Specifies which "*" in the <that> pattern to match.
        
                <thatstar> elements are similar to <star> elements, except
                that where <star/> returns the portion of the input string
                matched by a "*" character in the pattern, <thatstar/> returns
                the portion of the previous input string that was matched by a
                "*" in the current category's <that> pattern.
        
                """
                try:
                    index = int(elem[1]['index'])
                except KeyError:
                    index = 1
                # fetch the user's last input
                inputStack = self.get_predicate(self._inputStack, sessionID)
                input = self._subbers['normal'].sub(inputStack[-1])
                # fetch the Kernel's last response (for 'that' context)
                outputHistory = self.get_predicate(self._outputHistory, sessionID)
                try:
                    that = self._subbers['normal'].sub(outputHistory[-1])
                except:
                    that = ""  # there might not be any output yet
                topic = self.get_predicate("topic", sessionID)
                response = self._brain.star("thatstar", input, that, topic, index)
                return response
        
            # <think>
            def _processThink(self, elem, sessionID):
                """Process a <think> AIML element.
        
                <think> elements process their contents recursively, and then
                discard the results and return the empty string.  They're
                useful for setting predicates and learning AIML files without
                generating any output.
        
                """
                for e in elem[2:]:
                    self._processElement(e, sessionID)
                return ""
        
            # <topicstar>
            def _processTopicstar(self, elem, sessionID):
                """Process a <topicstar> AIML element.
        
                Optional element attributes:
                    index: Specifies which "*" in the <topic> pattern to match.
        
                <topicstar> elements are similar to <star> elements, except
                that where <star/> returns the portion of the input string
                matched by a "*" character in the pattern, <topicstar/>
                returns the portion of current topic string that was matched
                by a "*" in the current category's <topic> pattern.
        
                """
                try:
                    index = int(elem[1]['index'])
                except KeyError:
                    index = 1
                # fetch the user's last input
                inputStack = self.get_predicate(self._inputStack, sessionID)
                input = self._subbers['normal'].sub(inputStack[-1])
                # fetch the Kernel's last response (for 'that' context)
                outputHistory = self.get_predicate(self._outputHistory, sessionID)
                try:
                    that = self._subbers['normal'].sub(outputHistory[-1])
                except:
                    that = ""  # there might not be any output yet
                topic = self.get_predicate("topic", sessionID)
                response = self._brain.star("topicstar", input, that, topic, index)
                return response
        
            # <uppercase>
            def _processUppercase(self, elem, sessionID):
                """Process an <uppercase> AIML element.
        
                <uppercase> elements process their contents recursively, and
                return the results with all lower-case characters converted to
                upper-case.
        
                """
                response = ""
                for e in elem[2:]:
                    response += self._processElement(e, sessionID)
                return string.upper(response)
        
            # <version>
            def _processVersion(self, elem, sessionID):
                """Process a <version> AIML element.
        
                <version> elements return the version number of the AIML
                interpreter.
        
                """
                return self.version()
        
        ##################################################
        ### Self-test functions follow                 ###
        ##################################################
        def _testTag(kern, tag, input, outputList):
            """Tests 'tag' by feeding the Kernel 'input'.  If the result
            matches any of the strings in 'outputList', the test passes.
        
            """
            global _numTests, _numPassed
            _numTests += 1
            print "Testing <" + tag + ">:",
            response = kern.respond(input).decode(kern._textEncoding)
            if response in outputList:
                print "PASSED"
                _numPassed += 1
                return True
            else:
                print "FAILED (response: '%s')" % response.encode(kern._textEncoding, 'replace')
                return False
        
        if __name__ == "__main__":
            # Run some self-tests
            k = Kernel()
            k.bootstrap(learnFiles="self-test.aiml")
        
            global _numTests, _numPassed
            _numTests = 0
            _numPassed = 0
        
            _testTag(k, 'bot', 'test bot', ["My name is Nameless"])
        
            k.set_predicate('gender', 'male')
            _testTag(k, 'condition test #1', 'test condition name value', ['You are handsome'])
            k.set_predicate('gender', 'female')
            _testTag(k, 'condition test #2', 'test condition name value', [''])
            _testTag(k, 'condition test #3', 'test condition name', ['You are beautiful'])
            k.set_predicate('gender', 'robot')
            _testTag(k, 'condition test #4', 'test condition name', ['You are genderless'])
            _testTag(k, 'condition test #5', 'test condition', ['You are genderless'])
            k.set_predicate('gender', 'male')
            _testTag(k, 'condition test #6', 'test condition', ['You are handsome'])
        
            # the date test will occasionally fail if the original and "test"
            # times cross a second boundary.  There's no good way to avoid
            # this problem and still do a meaningful test, so we simply
            # provide a friendly message to be printed if the test fails.
            date_warning = """
            NOTE: the <date> test will occasionally report failure even if it
            succeeds.  So long as the response looks like a date/time string,
            there's nothing to worry about.
            """
            if not _testTag(k, 'date', 'test date', ["The date is %s" % time.asctime()]):
                print date_warning
        
            _testTag(k, 'formal', 'test formal', ["Formal Test Passed"])
            _testTag(k, 'gender', 'test gender', ["He'd told her he heard that her hernia is history"])
            _testTag(k, 'get/set', 'test get and set', ["I like cheese. My favorite food is cheese"])
            _testTag(k, 'gossip', 'test gossip', ["Gossip is not yet implemented"])
            _testTag(k, 'id', 'test id', ["Your id is _global"])
            _testTag(k, 'input', 'test input', ['You just said: test input'])
            _testTag(k, 'javascript', 'test javascript', ["Javascript is not yet implemented"])
            _testTag(k, 'lowercase', 'test lowercase', ["The Last Word Should Be lowercase"])
            _testTag(k, 'person', 'test person', ['HE think i knows that my actions threaten him and his.'])
            _testTag(k, 'person2', 'test person2', ['YOU think me know that my actions threaten you and yours.'])
            _testTag(k, 'person2 (no contents)', 'test person2 I Love Lucy', ['YOU Love Lucy'])
            _testTag(k, 'random', 'test random', ["response #1", "response #2", "response #3"])
            _testTag(k, 'random empty', 'test random empty', ["Nothing here!"])
            _testTag(k, 'sentence', "test sentence", ["My first letter should be capitalized."])
            _testTag(k, 'size', "test size", ["I've learned %d categories" % k.num_categories()])
            _testTag(k, 'sr', "test sr test srai", ["srai results: srai test passed"])
            _testTag(k, 'sr nested', "test nested sr test srai", ["srai results: srai test passed"])
            _testTag(k, 'srai', "test srai", ["srai test passed"])
            _testTag(k, 'srai infinite', "test srai infinite", [""])
            _testTag(k, 'star test #1', 'You should test star begin', ['Begin star matched: You should'])
            _testTag(k, 'star test #2', 'test star creamy goodness middle', ['Middle star matched: creamy goodness'])
            _testTag(k, 'star test #3', 'test star end the credits roll', ['End star matched: the credits roll'])
            _testTag(k, 'star test #4', 'test star having multiple stars in a pattern makes me extremely happy',
                     ['Multiple stars matched: having, stars in a pattern, extremely happy'])
            _testTag(k, 'system', "test system", ["The system says hello!"])
            _testTag(k, 'that test #1', "test that", ["I just said: The system says hello!"])
            _testTag(k, 'that test #2', "test that", ["I have already answered this question"])
            _testTag(k, 'thatstar test #1', "test thatstar", ["I say beans"])
            _testTag(k, 'thatstar test #2', "test thatstar", ["I just said \"beans\""])
            _testTag(k, 'thatstar test #3', "test thatstar multiple", ['I say beans and franks for everybody'])
            _testTag(k, 'thatstar test #4', "test thatstar multiple", ['Yes, beans and franks for all!'])
            _testTag(k, 'think', "test think", [""])
            k.set_predicate("topic", "fruit")
            _testTag(k, 'topic', "test topic", ["We were discussing apples and oranges"])
            k.set_predicate("topic", "Soylent Green")
            _testTag(k, 'topicstar test #1', 'test topicstar', ["Solyent Green is made of people!"])
            k.set_predicate("topic", "Soylent Ham and Cheese")
            _testTag(k, 'topicstar test #2', 'test topicstar multiple', ["Both Soylents Ham and Cheese are made of people!"])
            _testTag(k, 'unicode support', u"ÔÇÉÏºÃ", [u"Hey, you speak Chinese! ÔÇÉÏºÃ"])
            _testTag(k, 'uppercase', 'test uppercase', ["The Last Word Should Be UPPERCASE"])
            _testTag(k, 'version', 'test version', ["PyAIML is version %s" % k.version()])
            _testTag(k, 'whitespace preservation', 'test whitespace',
                     ["Extra   Spaces\n   Rule!   (but not in here!)    But   Here   They   Do!"])
        
            # Report test results
            print "--------------------"
            if _numTests == _numPassed:
                print "%d of %d tests passed!" % (_numPassed, _numTests)
            else:
                print "%d of %d tests passed (see above for detailed errors)" % (_numPassed, _numTests)
        
                # Run an interactive interpreter
                # print "\nEntering interactive mode (ctrl-c to exit)"
                # while True: print k.respond(raw_input("> "))
        ```
        </details>

      * `PatternMgr.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        # This class implements the AIML pattern-matching algorithm described
        # by Dr. Richard Wallace at the following site:
        # http://www.alicebot.org/documentation/matching.html
        
        import marshal
        import pprint
        import re
        import string
        import sys
        
        class PatternMgr:
            # special dictionary keys
            _UNDERSCORE = 0
            _STAR = 1
            _TEMPLATE = 2
            _THAT = 3
            _TOPIC = 4
            _BOT_NAME = 5
        
            def __init__(self):
                self._root = {}
                self._templateCount = 0
                self._botName = u"Nameless"
                punctuation = "\"`~!@#$%^&*()-_=+[{]}\|;:',<.>/?"
                self._puncStripRE = re.compile("[" + re.escape(punctuation) + "]")
                self._whitespaceRE = re.compile("\s+", re.LOCALE | re.UNICODE)
        
            def numTemplates(self):
                """Return the number of templates currently stored."""
                return self._templateCount
        
            def setBotName(self, name):
                """Set the name of the bot, used to match <bot name="name"> tags in
                patterns.  The name must be a single word!
        
                """
                # Collapse a multi-word name into a single word
                self._botName = unicode(string.join(name.split()))
        
            def dump(self):
                """Print all learned patterns, for debugging purposes."""
                pprint.pprint(self._root)
        
            def save(self, filename):
                """Dump the current patterns to the file specified by filename.  To
                restore later, use restore().
        
                """
                try:
                    outFile = open(filename, "wb")
                    marshal.dump(self._templateCount, outFile)
                    marshal.dump(self._botName, outFile)
                    marshal.dump(self._root, outFile)
                    outFile.close()
                except Exception, e:
                    print "Error saving PatternMgr to file %s:" % filename
                    raise Exception, e
        
            def restore(self, filename):
                """Restore a previously save()d collection of patterns."""
                try:
                    inFile = open(filename, "rb")
                    self._templateCount = marshal.load(inFile)
                    self._botName = marshal.load(inFile)
                    self._root = marshal.load(inFile)
                    inFile.close()
                except Exception, e:
                    print "Error restoring PatternMgr from file %s:" % filename
                    raise Exception, e
        
            def add(self, (pattern, that, topic), template):
                """Add a [pattern/that/topic] tuple and its corresponding template
                to the node tree.
        
                """
                # TODO: make sure words contains only legal characters
                # (alphanumerics,*,_)
        
                # Navigate through the node tree to the template's location, adding
                # nodes if necessary.
                node = self._root
                for word in string.split(pattern):
                    key = word
                    if key == u"_":
                        key = self._UNDERSCORE
                    elif key == u"*":
                        key = self._STAR
                    elif key == u"BOT_NAME":
                        key = self._BOT_NAME
                    if not node.has_key(key):
                        node[key] = {}
                    node = node[key]
        
                # navigate further down, if a non-empty "that" pattern was included
                if len(that) > 0:
                    if not node.has_key(self._THAT):
                        node[self._THAT] = {}
                    node = node[self._THAT]
                    for word in string.split(that):
                        key = word
                        if key == u"_":
                            key = self._UNDERSCORE
                        elif key == u"*":
                            key = self._STAR
                        if not node.has_key(key):
                            node[key] = {}
                        node = node[key]
        
                # navigate yet further down, if a non-empty "topic" string was included
                if len(topic) > 0:
                    if not node.has_key(self._TOPIC):
                        node[self._TOPIC] = {}
                    node = node[self._TOPIC]
                    for word in string.split(topic):
                        key = word
                        if key == u"_":
                            key = self._UNDERSCORE
                        elif key == u"*":
                            key = self._STAR
                        if not node.has_key(key):
                            node[key] = {}
                        node = node[key]
        
                # add the template.
                if not node.has_key(self._TEMPLATE):
                    self._templateCount += 1
                node[self._TEMPLATE] = template
        
            def match(self, pattern, that, topic):
                """Return the template which is the closest match to pattern. The
                'that' parameter contains the bot's previous response. The 'topic'
                parameter contains the current topic of conversation.
        
                Returns None if no template is found.
        
                """
                if len(pattern) == 0:
                    return None
                # Mutilate the input.  Remove all punctuation and convert the
                # text to all caps.
                input = string.upper(pattern)
                input = re.sub(self._puncStripRE, " ", input)
                if that.strip() == u"": that = u"ULTRABOGUSDUMMYTHAT"  # 'that' must never be empty
                thatInput = string.upper(that)
                thatInput = re.sub(self._puncStripRE, " ", thatInput)
                thatInput = re.sub(self._whitespaceRE, " ", thatInput)
                if topic.strip() == u"": topic = u"ULTRABOGUSDUMMYTOPIC"  # 'topic' must never be empty
                topicInput = string.upper(topic)
                topicInput = re.sub(self._puncStripRE, " ", topicInput)
        
                # Pass the input off to the recursive call
                patMatch, template = self._match(input.split(), thatInput.split(), topicInput.split(), self._root)
                return template
        
            def star(self, starType, pattern, that, topic, index):
                """Returns a string, the portion of pattern that was matched by a *.
        
                The 'starType' parameter specifies which type of star to find.
                Legal values are:
                 - 'star': matches a star in the main pattern.
                 - 'thatstar': matches a star in the that pattern.
                 - 'topicstar': matches a star in the topic pattern.
        
                """
                # Mutilate the input.  Remove all punctuation and convert the
                # text to all caps.
                input = string.upper(pattern)
                input = re.sub(self._puncStripRE, " ", input)
                input = re.sub(self._whitespaceRE, " ", input)
                if that.strip() == u"": that = u"ULTRABOGUSDUMMYTHAT"  # 'that' must never be empty
                thatInput = string.upper(that)
                thatInput = re.sub(self._puncStripRE, " ", thatInput)
                thatInput = re.sub(self._whitespaceRE, " ", thatInput)
                if topic.strip() == u"": topic = u"ULTRABOGUSDUMMYTOPIC"  # 'topic' must never be empty
                topicInput = string.upper(topic)
                topicInput = re.sub(self._puncStripRE, " ", topicInput)
                topicInput = re.sub(self._whitespaceRE, " ", topicInput)
        
                # Pass the input off to the recursive pattern-matcher
                patMatch, template = self._match(input.split(), thatInput.split(), topicInput.split(), self._root)
                if template == None:
                    return ""
        
                # Extract the appropriate portion of the pattern, based on the
                # starType argument.
                words = None
                if starType == 'star':
                    patMatch = patMatch[:patMatch.index(self._THAT)]
                    words = input.split()
                elif starType == 'thatstar':
                    patMatch = patMatch[patMatch.index(self._THAT) + 1: patMatch.index(self._TOPIC)]
                    words = thatInput.split()
                elif starType == 'topicstar':
                    patMatch = patMatch[patMatch.index(self._TOPIC) + 1:]
                    words = topicInput.split()
                else:
                    # unknown value
                    raise ValueError, "starType must be in ['star', 'thatstar', 'topicstar']"
        
                # compare the input string to the matched pattern, word by word.
                # At the end of this loop, if foundTheRightStar is true, start and
                # end will contain the start and end indices (in "words") of
                # the substring that the desired star matched.
                foundTheRightStar = False
                start = end = j = numStars = k = 0
                for i in range(len(words)):
                    # This condition is true after processing a star
                    # that ISN'T the one we're looking for.
                    if i < k:
                        continue
                    # If we're reached the end of the pattern, we're done.
                    if j == len(patMatch):
                        break
                    if not foundTheRightStar:
                        if patMatch[j] in [self._STAR, self._UNDERSCORE]:  # we got a star
                            numStars += 1
                            if numStars == index:
                                # This is the star we care about.
                                foundTheRightStar = True
                            start = i
                            # Iterate through the rest of the string.
                            for k in range(i, len(words)):
                                # If the star is at the end of the pattern,
                                # we know exactly where it ends.
                                if j + 1 == len(patMatch):
                                    end = len(words)
                                    break
                                # If the words have started matching the
                                # pattern again, the star has ended.
                                if patMatch[j + 1] == words[k]:
                                    end = k - 1
                                    i = k
                                    break
                        # If we just finished processing the star we cared
                        # about, we exit the loop early.
                        if foundTheRightStar:
                            break
                    # Move to the next element of the pattern.
                    j += 1
        
                # extract the star words from the original, unmutilated input.
                if foundTheRightStar:
                    # print string.join(pattern.split()[start:end+1])
                    if starType == 'star':
                        return string.join(pattern.split()[start:end + 1])
                    elif starType == 'thatstar':
                        return string.join(that.split()[start:end + 1])
                    elif starType == 'topicstar':
                        return string.join(topic.split()[start:end + 1])
                else:
                    return ""
        
            def _match(self, words, thatWords, topicWords, root):
                """Return a tuple (pat, tem) where pat is a list of nodes, starting
                at the root and leading to the matching pattern, and tem is the
                matched template.
        
                """
                # base-case: if the word list is empty, return the current node's
                # template.
                if len(words) == 0:
                    # we're out of words.
                    pattern = []
                    template = None
                    if len(thatWords) > 0:
                        # If thatWords isn't empty, recursively
                        # pattern-match on the _THAT node with thatWords as words.
                        try:
                            pattern, template = self._match(thatWords, [], topicWords, root[self._THAT])
                            if pattern != None:
                                pattern = [self._THAT] + pattern
                        except KeyError:
                            pattern = []
                            template = None
                    elif len(topicWords) > 0:
                        # If thatWords is empty and topicWords isn't, recursively pattern
                        # on the _TOPIC node with topicWords as words.
                        try:
                            pattern, template = self._match(topicWords, [], [], root[self._TOPIC])
                            if pattern != None:
                                pattern = [self._TOPIC] + pattern
                        except KeyError:
                            pattern = []
                            template = None
                    if template == None:
                        # we're totally out of input.  Grab the template at this node.
                        pattern = []
                        try:
                            template = root[self._TEMPLATE]
                        except KeyError:
                            template = None
                    return (pattern, template)
        
                first = words[0]
                suffix = words[1:]
        
                # Check underscore.
                # Note: this is causing problems in the standard AIML set, and is
                # currently disabled.
                if root.has_key(self._UNDERSCORE):
                    # Must include the case where suf is [] in order to handle the case
                    # where a * or _ is at the end of the pattern.
                    for j in range(len(suffix) + 1):
                        suf = suffix[j:]
                        pattern, template = self._match(suf, thatWords, topicWords, root[self._UNDERSCORE])
                        if template is not None:
                            newPattern = [self._UNDERSCORE] + pattern
                            return (newPattern, template)
        
                # Check first
                if root.has_key(first):
                    pattern, template = self._match(suffix, thatWords, topicWords, root[first])
                    if template is not None:
                        newPattern = [first] + pattern
                        return (newPattern, template)
        
                # check bot name
                if root.has_key(self._BOT_NAME) and first == self._botName:
                    pattern, template = self._match(suffix, thatWords, topicWords, root[self._BOT_NAME])
                    if template is not None:
                        newPattern = [first] + pattern
                        return (newPattern, template)
        
                # check star
                if root.has_key(self._STAR):
                    # Must include the case where suf is [] in order to handle the case
                    # where a * or _ is at the end of the pattern.
                    for j in range(len(suffix) + 1):
                        suf = suffix[j:]
                        pattern, template = self._match(suf, thatWords, topicWords, root[self._STAR])
                        if template is not None:
                            newPattern = [self._STAR] + pattern
                            return (newPattern, template)
        
                # No matches were found.
                return (None, None)
        ```
        </details>

      * `Utils.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        """This file contains assorted general utility functions used by other
        modules in the PyAIML package.
        
        """
        
        def sentences(s):
            """Split the string s into a list of sentences."""
            try:
                s + ""
            except TypeError:
                print "s must be a string"
            pos = 0
            sentence_list = []
            l = len(s)
            while pos < l:
                try:
                    p = s.index('.', pos)
                except:
                    p = l + 1
                try:
                    q = s.index('?', pos)
                except:
                    q = l + 1
                try:
                    e = s.index('!', pos)
                except:
                    e = l + 1
                end = min(p, q, e)
                sentence_list.append(s[pos:end].strip())
                pos = end + 1
            # If no sentences were found, return a one-item list containing
            # the entire input string.
            if len(sentence_list) == 0: sentence_list.append(s)
            return sentence_list
        
        # Self test
        if __name__ == "__main__":
            # sentences
            sents = sentences("First.  Second, still?  Third and Final!  Well, not really")
            assert (len(sents) == 4)
        ```
        </details>

      * `WordSub.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        """This module implements the WordSub class, modelled after a recipe
        in "Python Cookbook" (Recipe 3.14, "Replacing Multiple Patterns in a
        Single Pass" by Xavier Defrang).
        
        Usage:
        Use this class like a dictionary to add before/after pairs:
            > subber = TextSub()
            > subber["before"] = "after"
            > subber["begin"] = "end"
        Use the sub() method to perform the substitution:
            > print subber.sub("before we begin")
            after we end
        All matching is intelligently case-insensitive:
            > print subber.sub("Before we BEGIN")
            After we END
        The 'before' words must be complete words -- no prefixes.
        The following example illustrates this point:
            > subber["he"] = "she"
            > print subber.sub("he says he'd like to help her")
            she says she'd like to help her
        Note that "he" and "he'd" were replaced, but "help" and "her" were
        not.
        """
        
        # 'dict' objects weren't available to subclass from until version 2.2.
        # Get around this by importing UserDict.UserDict if the built-in dict
        # object isn't available.
        try:
            dict
        except:
            from UserDict import UserDict as dict
        
        import re
        import string
        
        class WordSub(dict):
            """All-in-one multiple-string-substitution class."""
        
            def _word_to_regex(self, word):
                """Convert a word to a regex object which matches the word."""
                if word != "" and word[0].isalpha() and word[-1].isalpha():
                    return "\\b%s\\b" % re.escape(word)
                else:
                    return r"\b%s\b" % re.escape(word)
        
            def _update_regex(self):
                """Build re object based on the keys of the current
                dictionary.
        
                """
                self._regex = re.compile("|".join(map(self._word_to_regex, self.keys())))
                self._regexIsDirty = False
        
            def __init__(self, defaults={}):
                """Initialize the object, and populate it with the entries in
                the defaults dictionary.
        
                """
                self._regex = None
                self._regexIsDirty = True
                for k, v in defaults.items():
                    self[k] = v
        
            def __call__(self, match):
                """Handler invoked for each regex match."""
                return self[match.group(0)]
        
            def __setitem__(self, i, y):
                self._regexIsDirty = True
                # for each entry the user adds, we actually add three entrys:
                super(type(self), self).__setitem__(string.lower(i), string.lower(y))  # key = value
                super(type(self), self).__setitem__(string.capwords(i), string.capwords(y))  # Key = Value
                super(type(self), self).__setitem__(string.upper(i), string.upper(y))  # KEY = VALUE
        
            def sub(self, text):
                """Translate text, returns the modified text."""
                if self._regexIsDirty:
                    self._update_regex()
                return self._regex.sub(self, text)
        
        # self-test
        if __name__ == "__main__":
            subber = WordSub()
            subber["apple"] = "banana"
            subber["orange"] = "pear"
            subber["banana"] = "apple"
            subber["he"] = "she"
            subber["I'd"] = "I would"
        
            # test case insensitivity
            inStr = "I'd like one apple, one Orange and one BANANA."
            outStr = "I Would like one banana, one Pear and one APPLE."
            if subber.sub(inStr) == outStr:
                print "Test #1 PASSED"
            else:
                print "Test #1 FAILED: '%s'" % subber.sub(inStr)
        
            inStr = "He said he'd like to go with me"
            outStr = "She said she'd like to go with me"
            if subber.sub(inStr) == outStr:
                print "Test #2 PASSED"
            else:
                print "Test #2 FAILED: '%s'" % subber.sub(inStr)
        ```
        </details>

      * `__init__.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        __all__ = []
        
        # The Kernel class is the only class most implementations should need.
        from Kernel import Kernel
        ```
        </details>

      * `dialogue_manager.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        import os
        from Kernel import Kernel
        import argparse
        import signal
        import slu_utils
        from event_abstract import *
        import datetime
        
        class DialogueManager(EventAbstractClass):
            PATH = ''
            RANKED_EVENT = "VRanked"
            DIALOGUE_REQUEST_EVENT = "DialogueVequest"
            cocktail_data = {}
            location = {}
            order_counter = 0
        
            def __init__(self, ip, port, aiml_path):
                super(self.__class__, self).__init__(self, ip, port)
        
                self.__shutdown_requested = False
                signal.signal(signal.SIGINT, self.signal_handler)
        
                self.kernel = Kernel()
                self.__learn(aiml_path)
        
            def start(self, *args, **kwargs):
                self.subscribe(
                    event=DialogueManager.RANKED_EVENT,
                    callback=self.ranked_callback
                )
                self.subscribe(
                    event=DialogueManager.DIALOGUE_REQUEST_EVENT,
                    callback=self.request_callback
                )
        
                print "[" + self.inst.__class__.__name__ + "] Subscribers:", self.memory.getSubscribers(
                    DialogueManager.RANKED_EVENT)
                print "[" + self.inst.__class__.__name__ + "] Subscribers:", self.memory.getSubscribers(
                    DialogueManager.DIALOGUE_REQUEST_EVENT)
        
                self._spin()
        
                self.unsubscribe(DialogueManager.RANKED_EVENT)
                self.unsubscribe(DialogueManager.DIALOGUE_REQUEST_EVENT)
                self.broker.shutdown()
        
            def ranked_callback(self, *args, **kwargs):
                transcriptions_dict = slu_utils.list_to_dict_w_probabilities(args[1])
                best_transcription = slu_utils.pick_best(transcriptions_dict)
                print "[" + self.inst.__class__.__name__ + "] User says: " + best_transcription
                reply = self.kernel.respond(best_transcription)
                self.do_something(reply)
                #print "[" + self.inst.__class__.__name__ + "] Robot says: " + reply
                #self.memory.raiseEvent("Veply", reply)
        
            def request_callback(self, *args, **kwargs):
                splitted = args[1].split('_')
                to_send = ' '.join(splitted)
                if 'start' in splitted:
                    self.memory.raiseEvent("ASR_enable", 1)
                if 'missingdrink' in splitted:
                    customer = self.cocktail_data.get(splitted[1], None)['customer']
                    drink = self.cocktail_data[splitted[1]]['drink']
                    to_send = 'missingdrink customer ' + customer + ' drink ' + drink + ' '+ splitted[2]
                print to_send
                reply = self.kernel.respond(to_send)
                print reply
                self.do_something(reply)
        
            def _spin(self, *args):
                while not self.__shutdown_requested:
                    for f in args:
                        f()
                    time.sleep(.1)
        
            def signal_handler(self, signal, frame):
                print "[" + self.inst.__class__.__name__ + "] Caught Ctrl+C, stopping."
                self.__shutdown_requested = True
                print "[" + self.inst.__class__.__name__ + "] Good-bye"
        
            def __learn(self, path):
                for root, directories, file_names in os.walk(path):
                    for filename in file_names:
                        if filename.endswith('.aiml'):
                            self.kernel.learn(os.path.join(root, filename))
                print "[" + self.inst.__class__.__name__ + "] Number of categories: " + str(self.kernel.num_categories())
        
            def do_something(self, message):
                splitted = message.split('|')
                for submessage in splitted:
                    if '[SAY]' in submessage:
                        reply = submessage.replace('[SAY]', '').strip()
                        print "[" + self.inst.__class__.__name__ + "] Robot says: " + reply
                        self.memory.raiseEvent("Veply", reply)
                    elif '[TAKEORDERDATA]' in submessage:
                        data = submessage.replace('[TAKEORDERDATA]', '').replace(')', '').strip()
                        customer, drink = data.split('(')
                        temp = {}
                        temp['drink'] = drink
                        temp['customer'] = customer
                        self.cocktail_data[str(self.order_counter)] = temp
                        print self.cocktail_data
                        self.memory.raiseEvent("DialogueVesponse", self.cocktail_data)
                        self.order_counter = self.order_counter + 1
                    elif '[DRINKSALTERNATIVES]' in submessage:
                        data = submessage.replace('[DRINKSALTERNATIVES]', '').replace(')', '').strip()
                        # search for drinks from the drinks' list
                    elif '[LOOKFORDATA]' in submessage:
                        data = submessage.replace('[TAKEORDERDATA]', '').strip()
                        self.location['location'] = data
                        self.memory.raiseEvent("DialogueVesponse", self.location)
                    elif '[WHATSTHETIME]' in submessage:
                        now = datetime.datetime.now()
                        reply = "It's " + str(now.hour )+ " " + str(now.minute)
                        print "[" + self.inst.__class__.__name__ + "] Robot says: " + reply
                        self.memory.raiseEvent("Veply", reply)
                    elif '[STOP]' in submessage:
                        self.memory.raiseEvent("ASR_enable", 1)
                    else:
                        print submessage
        
        def main():
            parser = argparse.ArgumentParser()
        
            parser.add_argument("-i", "--pip", type=str, default="127.0.0.1",
                                help="Robot ip address")
            parser.add_argument("-p", "--pport", type=int, default=9559,
                                help="Robot port number")
            parser.add_argument("-a", "--aiml-path", type=str, default="resources/aiml_kbs/spqrel",
                                help="Path to the root folder of AIML Knowledge Base")
            args = parser.parse_args()
        
            dm = DialogueManager(
                ip=args.pip,
                port=args.pport,
                aiml_path=args.aiml_path
            )
            dm.update_globals(globals())
            dm.start()
        
        if __name__ == "__main__":
            main()
        ```
        </details>

    * `event_abstract.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      import time
      from naoqi import ALProxy, ALBroker, ALModule
      from abc import ABCMeta, abstractmethod
      
      class EventAbstractClass(ALModule):
          __metaclass__ = ABCMeta
      
          def __init__(self, inst, ip, port):
              self.inst = inst
              self.name = inst.__class__.__name__ + "_inst"
              self._make_global(self.name, self)
              self.broker = self._connect(self.name, ip, port)
              super(EventAbstractClass, self).__init__(self.name)
      
              self.memory = self._make_global("memory", ALProxy("ALMemory"))
      
          def _connect(self, name, ip, port):
              try:
                  broker = ALBroker(name + "_broker",
                                    "0.0.0.0",  # listen to anyone
                                    0,  # find a free port and use it
                                    ip,  # parent broker IP
                                    port)
                  print "Connected to %s:%s" % (ip, str(port))
                  return broker
              except RuntimeError:
                  print "Cannot connect to %s:%s. Retrying in 1 second." % (ip, str(port))
                  time.sleep(1)
                  return self._connect(name, ip, port)
      
          def _make_global(self, name, var):
              globals()[name] = var
              return globals()[name]
      
          def update_globals(self, glob):
              glob[self.name] = self
      
          def subscribe(self, event, callback):
              self.memory.subscribeToEvent(
                  event,
                  self.name,
                  callback.func_name
              )
      
          def unsubscribe(self, event):
              self.memory.unsubscribeToEvent(
                  event,
                  self.name
              )
      
          def remove_subscribers(self, event):
              subscribers = self.memory.getSubscribers(event)
              if subscribers:
                  print event + " already in use by another node"
                  for module in subscribers:
                      self.__stop_module(module, event)
      
          def __stop_module(self, module, event):
              print "Unsubscribing '{}' from " + event.format(
                  module)
              try:
                  self.memory.unsubscribeToEvent(event, module)
              except RuntimeError:
                  print "Could not unsubscribe from " + event
      
          @abstractmethod
          def start(self, *args, **kwargs):
              """
              All subscribing goes in here.
              """
              return
      ```
      </details>

    * **language_understanding/**
      * `__init__.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown

        ```
        </details>

      * `lu4r_client.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        import requests
        import json
        
        class LU4RClient:
            ip = '127.0.0.1'  # class variable shared by all instances
            port = 9001
            chain_type = ''
            output_type = ''
            language = ''
            HEADERS = {'content-type': 'application/json'}
        
            LU4R_STATUS_URL = ''
            LU4R_INFO_URL = ''
            LU4R_PARSE_URL = ''
        
            json_entities = ''
        
            def __init__(self, lip, lport):
                self.ip = lip
                self.port = lport
        
                self.LU4R_STATUS_URL = 'http://' + self.ip + ':' + str(self.port) + '/init/status'
                self.LU4R_INFO_URL = 'http://' + self.ip + ':' + str(self.port) + '/init/info'
                self.LU4R_PARSE_URL = 'http://' + self.ip + ':' + str(self.port) + '/service/slu'
        
                self.status()
        
                info = json.loads(self.info())
                self.chain_type = info['chain_type']
                self.output_type = info['output_type']
                self.language = info['language']
        
            def status(self):
                try:
                    response = requests.post(self.LU4R_STATUS_URL, {}, headers=self.HEADERS)
                    print "[" + self.inst.__class__.__name__ + "]" + response.text
                    return 1
                except requests.exceptions.RequestException as e:
                    print "[" + self.inst.__class__.__name__ + "] [STATUS]ERROR! LU4R is not running. Launch it and retry."
                    return 0
        
            def info(self):
                try:
                    response = requests.post(self.LU4R_INFO_URL, {}, headers=self.HEADERS)
                    return response.text
                except requests.exceptions.RequestException as e:
                    print "[" + self.inst.__class__.__name__ + "] [INFO]ERROR! LU4R is not running. Launch it and retry."
                    return 0
        
            def parse_sentence(self, sentence):
                try:
                    json_hypo = '{\"hypotheses\":[{\"transcription\":\"' + sentence + '\", \"confidence\":"1.0\",\"rank\":\"1\"}]}'
                    to_send = {'hypo': json_hypo, 'entities': '{\"entities\":[' + self.json_entities + ']}'}
                    response = requests.post(self.LU4R_PARSE_URL, to_send, headers=self.HEADERS)
                    return response.text
                except requests.exceptions.RequestException as e:
                    print "[" + self.inst.__class__.__name__ + "] [PARSE]ERROR! LU4R cannot to parse."
        
            def parse_sentences(self, sentences):
                sentences_dict = []
                counter = 1;
                for sentence in sentences:
                    hypothesis = dict()
                    hypothesis['transcription'] = sentence
                    hypothesis['confidence'] = str(1 - (float(counter) / float(len(sentences))))
                    hypothesis['rank'] = str(counter)
                    sentences_dict.append(hypothesis)
                    counter = counter + 1
                try:
                    json_hypo = '{\"hypotheses\":' + json.dumps(sentences_dict) + '}'
                    to_send = {'hypo': json_hypo, 'entities': '{\"entities\":[' + self.json_entities + ']}'}
                    response = requests.post(self.LU4R_PARSE_URL, to_send, headers=self.HEADERS)
                    return response.text
                except requests.exceptions.RequestException as e:
                    print "[" + self.inst.__class__.__name__ + "] [PARSE]ERROR! LU4R cannot to parse."
        
            def parse_sentence_perceptual(self, sentence, entities):
                if self.chain_type != 'SIMPLE':
                    print "[" + self.inst.__class__.__name__ + "] [WARNING]BASIC chain active. Perceptual information will be neglected."
                self.json_entities = entities
                return self.parse_sentence(sentence)
        ```
        </details>

    * **resources/**
      * **aiml_kbs/**
        * **alice/**
        * **spqrel/**
    * `slu_utils.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      def lines_to_list(_file):
          with open(_file) as f:
              _list = f.readlines()
          return [x.strip() for x in _list]
      
      def normalize(sublist):
          m = .0
          for trans in sublist:
              m = m + sublist[trans]
          for trans in sublist:
              sublist[trans] = sublist[trans] / m
          return sublist
      
      def list_to_dict(transcriptions):
          d = {}
          for asr in transcriptions:
              counter = 0
              d[asr[0]] = {}
              for trans in asr[1]:
                  counter = counter + 1
                  d[asr[0]][trans] = counter
          return d
      
      def list_to_dict_w_probabilities(transcriptions):
          d = {}
          for asr in transcriptions:
              d[asr[0]] = {}
              for trans in asr[1]:
                  d[asr[0]][trans[0]] = trans[1]
          return d
      
      def pick_best(transcriptions):
          confidence = 1.1
          for asr in transcriptions:
              for hypo in transcriptions[asr]:
                  if transcriptions[asr][hypo] < confidence:
                      best_hypo = hypo
                      confidence = transcriptions[asr][hypo]
          return best_hypo
      ```
      </details>

    * **speech_reranking/**
      * `__init__.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown

        ```
        </details>

      * `reranker.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        import argparse
        import signal
        from slu_utils import *
        from event_abstract import *
        
        class ReRanker(EventAbstractClass):
            PATH = ''
            EVENT_NAME = "VordRecognized"
        
            def __init__(self, ip, port, alpha, noun_cost, verb_cost, grammar_cost, nuance_cost, noun_dictionary,
                         verb_dictionary, nuance_grammar):
                super(self.__class__, self).__init__(self, ip, port)
                self.alpha = alpha
                self.noun_cost = noun_cost
                self.verb_cost = verb_cost
                self.grammar_cost = grammar_cost
                self.nuance_cost = nuance_cost
                self.noun_dictionary = lines_to_list(noun_dictionary)
                self.verb_dictionary = lines_to_list(verb_dictionary)
                self.nuance_grammar = lines_to_list(nuance_grammar)
                self.__shutdown_requested = False
                signal.signal(signal.SIGINT, self.signal_handler)
        
            def start(self, *args, **kwargs):
                self.subscribe(
                    event=ReRanker.EVENT_NAME,
                    callback=self.callback
                )
        
                print "[" + self.inst.__class__.__name__ + "] Subscribers:", self.memory.getSubscribers(ReRanker.EVENT_NAME)
        
                self._spin()
        
                self.unsubscribe(ReRanker.EVENT_NAME)
                self.broker.shutdown()
        
            def callback(self, *args, **kwargs):
                print "[" + self.inst.__class__.__name__ + "] ReRanking.."
                temp = args[1]
                transcriptions = list_to_dict(temp)
                if 'GoogleASR' in transcriptions:
                    transcriptions = self.__re_rank(transcriptions)
                    print "[" + self.inst.__class__.__name__ + "] " + str(transcriptions)
                self.memory.raiseEvent("VRanked", transcriptions)
        
            def stop(self):
                self.__shutdown_requested = True
                print '[' + self.inst.__class__.__name__ + '] Good-bye'
        
            def _spin(self, *args):
                while not self.__shutdown_requested:
                    for f in args:
                        f()
                    time.sleep(.1)
        
            def signal_handler(self, signal, frame):
                print "[" + self.inst.__class__.__name__ + "] Caught Ctrl+C, stopping."
                self.__shutdown_requested = True
                print "[" + self.inst.__class__.__name__ + "] Good-bye"
        
            def __re_rank(self, transcriptions):
                """
                Perform the re-ranking of the transcriptions generated by the several ASRs.
                These are the steps:
                 - compute prior
                 - first evidence (noun elements)
                 - second evidence (verb elements)
                 - third evidence (grammar generated - if the sentence is present into the vocabulary defined for the NuanceASR)
                 - fourth evidence (recognized by grammar - if the sentence is present into the list of the sentences recognized
                   by the NuanceASR)
                :param transcriptions:
                :return: ordered dictionary
                """
                transcriptions = self.__compute_prior(transcriptions)
                transcriptions = self.__compute_noun_posterior(transcriptions)
                transcriptions = self.__compute_verb_posterior(transcriptions)
                transcriptions = self.__compute_grammar_posterior(transcriptions)
                transcriptions = self.__compute_overlap_posterior(transcriptions)
                return transcriptions
        
            def __compute_prior(self, transcriptions):
                for asr in transcriptions:
                    n = len(transcriptions[asr])
                    m = (n * (n + 1)) / 2
                    if len(transcriptions[asr]) > 1:
                        for trans in transcriptions[asr]:
                            transcriptions[asr][trans] = (float(transcriptions[asr][trans]) + float(self.alpha)) / (
                            float(m) + float(self.alpha * n))
                return transcriptions
        
            def __compute_noun_posterior(self, transcriptions):
                for asr in transcriptions:
                    if len(transcriptions[asr]) > 1:
                        for trans in transcriptions[asr]:
                            for noun in self.noun_dictionary:
                                if noun in trans:
                                    transcriptions[asr][trans] = transcriptions[asr][trans] * float(self.noun_cost)
                        transcriptions[asr] = normalize(transcriptions[asr])
                return transcriptions
        
            def __compute_verb_posterior(self, transcriptions):
                for asr in transcriptions:
                    if len(transcriptions[asr]) > 1:
                        for trans in transcriptions[asr]:
                            for noun in self.verb_dictionary:
                                if noun in trans:
                                    transcriptions[asr][trans] = transcriptions[asr][trans] * float(self.verb_cost)
                        transcriptions[asr] = normalize(transcriptions[asr])
                return transcriptions
        
            def __compute_grammar_posterior(self, transcriptions):
                for asr in transcriptions:
                    if len(transcriptions[asr]) > 1:
                        for trans in transcriptions[asr]:
                            for noun in self.nuance_grammar:
                                if noun in trans:
                                    transcriptions[asr][trans] = transcriptions[asr][trans] * float(self.grammar_cost)
                        transcriptions[asr] = normalize(transcriptions[asr])
                return transcriptions
        
            def __compute_overlap_posterior(self, transcriptions):
                for asr in transcriptions:
                    if len(transcriptions[asr]) > 1:
                        for trans in transcriptions[asr]:
                            if trans in transcriptions['NuanceASR']:
                                transcriptions[asr][trans] = transcriptions[asr][trans] * float(self.nuance_cost)
                        transcriptions[asr] = normalize(transcriptions[asr])
                return transcriptions
        
        def main():
            parser = argparse.ArgumentParser()
        
            parser.add_argument("-i", "--pip", type=str, default="127.0.0.1",
                                help="Robot ip address")
            parser.add_argument("-p", "--pport", type=int, default=9559,
                                help="Robot port number")
            parser.add_argument("-a", "--alpha", type=float, default=1.0,
                                help="Alpha parameter for the additive smoothing of the prior distribution")
            parser.add_argument("-n", "--noun-cost", type=float, default=.1,
                                help="Cost for the noun posterior distribution")
            parser.add_argument("-v", "--verb-cost", type=float, default=.7,
                                help="Cost for the verb posterior distribution")
            parser.add_argument("-g", "--grammar-cost", type=float, default=.1,
                                help="Cost for the grammar posterior distribution")
            parser.add_argument("-o", "--overlap-cost", type=float, default=.1,
                                help="Cost for the overlap posterior distribution")
            parser.add_argument("--noun-dictionary", type=str, default="resources/noun_dictionary.txt",
                                help="A txt file containing the list of domain nouns")
            parser.add_argument("--verb-dictionary", type=str, default="resources/verb_dictionary.txt",
                                help="A txt file containing the list of domain verbs")
            parser.add_argument("--nuance-grammar", type=str, default="resources/nuance_grammar.txt",
                                help="A txt file containing the list of sentences composing the vocabulary")
        
            args = parser.parse_args()
        
            rr = ReRanker(
                ip=args.pip,
                port=args.pport,
                alpha=args.alpha,
                noun_cost=args.noun_cost,
                verb_cost=args.verb_cost,
                grammar_cost=args.grammar_cost,
                nuance_cost=args.overlap_cost,
                noun_dictionary=args.noun_dictionary,
                verb_dictionary=args.verb_dictionary,
                nuance_grammar=args.nuance_grammar
            )
            rr.update_globals(globals())
            rr.start()
        
        if __name__ == "__main__":
            main()
        ```
        </details>

    * **speech_to_text/**
      * `__init__.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown

        ```
        </details>

      * `google_client.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        import os
        import urllib
        import requests
        import json
        import slu_utils
        
        class GoogleClient:
            timeout = 5
            url = ''
            headers = {"Content-Type": "audio/x-flac; rate=16000"}
        
            def __init__(self, language, key_file):
                keys = slu_utils.lines_to_list(key_file)
                q = {"output": "json", "lang": language, "key": keys[0]}
                self.url = "https://www.google.com/speech-api/v2/recognize?%s" % (urllib.urlencode(q))
        
            def recognize_file(self, file_path):
                try:
                    print "[" + self.__class__.__name__ + "] [GOOGLE] Recognizing.."
                    transcriptions = []
                    data = open(file_path, "rb").read()
                    response = requests.post(self.url, headers=self.headers, data=data, timeout=self.timeout)
                    json_units = response.text.split(os.linesep)
                    for unit in json_units:
                        if not unit:
                            continue
                        obj = json.loads(unit)
                        alternatives = obj["result"]
                        if len(alternatives) > 0:
                            for obj in alternatives:
                                results = obj["alternative"]
                                for result in results:
                                    transcriptions.append(result["transcript"])
                    return transcriptions
                except ValueError as ve:
                    print "[" + self.__class__.__name__ + "] [RECOGNIZE]ERROR! Google APIs are temporary unavailable. Returning empty list.."
                    return []
                except requests.exceptions.RequestException as e:
                    print "[" + self.__class__.__name__ + "] [RECOGNIZE]ERROR! Unable to reach Google. Returning empty list.."
                    return []
        
            def recognize_data(self, data):
                try:
                    print "[" + self.__class__.__name__ + "] [GOOGLE] Recognizing.."
                    transcriptions = []
                    response = requests.post(self.url, headers=self.headers, data=data, timeout=self.timeout)
                    json_units = response.text.split(os.linesep)
                    for unit in json_units:
                        if not unit:
                            continue
                        obj = json.loads(unit)
                        alternatives = obj["result"]
                        if len(alternatives) > 0:
                            for obj in alternatives:
                                results = obj["alternative"]
                                for result in results:
                                    transcriptions.append(result["transcript"])
                    return transcriptions
                except ValueError as ve:
                    print "[" + self.__class__.__name__ + "] [RECOGNIZE]ERROR! Google APIs are temporary unavailable. Returning empty list.."
                    return []
                except requests.exceptions.RequestException as e:
                    print "[" + self.__class__.__name__ + "] [RECOGNIZE]ERROR! Unable to reach Google. Returning empty list.."
                    return []
        
        def main():
            g = GoogleClient("en-US", "resources/google_keys.txt")
            while True:
                print g.recognize_file('resources/recording.flac')
        
        if __name__ == "__main__":
            main()
        ```
        </details>

      * `speech_recognition.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        import argparse
        import signal
        from naoqi import ALProxy, ALBroker, ALModule
        from google_client import *
        from event_abstract import *
        
        class SpeechRecognition(EventAbstractClass):
            WR_EVENT = "WordRecognized"
            TD_EVENT = "ALTextToSpeech/TextDone"
            ASR_ENABLE = "ASR_enable"
            FLAC_COMM = 'flac -f '
            FILE_PATH = '/tmp/recording'
            CHANNELS = [0, 0, 1, 0]
            timeout = 0
        
            def __init__(self, ip, port, language, word_spotting, audio, visual, vocabulary_file, google_keys):
                super(self.__class__, self).__init__(self, ip, port)
        
                self.__shutdown_requested = False
                signal.signal(signal.SIGINT, self.signal_handler)
        
                vocabulary = slu_utils.lines_to_list(vocabulary_file)
                if (language == 'en') or (language == 'eng') or (language == 'english') or (language == 'English'):
                    nuance_language = 'English'
                    google_language = "en-US"
        
                self.nuance_asr = ALProxy("ALSpeechRecognition")
        
                self.audio_recorder = ALProxy("ALAudioRecorder")
        
                self.google_asr = GoogleClient(google_language, google_keys)
        
                self.configure(
                    vocabulary=vocabulary,
                    nuance_language=nuance_language,
                    word_spotting=word_spotting,
                    audio=audio,
                    visual=visual
                )
        
            def start(self, *args, **kwargs):
                self.subscribe(
                    event=SpeechRecognition.WR_EVENT,
                    callback=self.word_recognized_callback
                )
                self.subscribe(
                    event=SpeechRecognition.TD_EVENT,
                    callback=self.text_done_callback
                )
        
                self.subscribe(
                    event=SpeechRecognition.ASR_ENABLE,
                    callback=self.enable_callback
                )
        
                print "[" + self.inst.__class__.__name__ + "] Subscribers:", self.memory.getSubscribers(
                    SpeechRecognition.WR_EVENT)
                print "[" + self.inst.__class__.__name__ + "] Subscribers:", self.memory.getSubscribers(
                    SpeechRecognition.TD_EVENT)
                print "[" + self.inst.__class__.__name__ + "] Subscribers:", self.memory.getSubscribers(
                    SpeechRecognition.ASR_ENABLE)
        
                self.is_enabled = True
        
                self.audio_recorder.stopMicrophonesRecording()
                self.audio_recorder.startMicrophonesRecording(self.FILE_PATH + ".wav", "wav", 16000, self.CHANNELS)
        
                self._spin()
        
                self.unsubscribe(SpeechRecognition.WR_EVENT)
                self.unsubscribe(SpeechRecognition.TD_EVENT)
                self.unsubscribe(SpeechRecognition.ASR_ENABLE)
                self.broker.shutdown()
        
            def stop(self):
                self.audio_recorder.stopMicrophonesRecording()
                self.is_enabled = False
                self.__shutdown_requested = True
                print '[' + self.inst.__class__.__name__ + '] Good-bye'
        
            def configure(self, word_spotting, nuance_language, audio, visual, vocabulary):
                self.nuance_asr.pause(True)
                self.nuance_asr.setVocabulary(vocabulary, word_spotting)
                self.nuance_asr.setLanguage(nuance_language)
                self.nuance_asr.setAudioExpression(audio)
                self.nuance_asr.setVisualExpression(visual)
                self.nuance_asr.pause(False)
        
            def word_recognized_callback(self, *args, **kwargs):
                self.audio_recorder.stopMicrophonesRecording()
                self.nuance_asr.pause(True)
                """
                Convert Wave file into Flac file
                """
                os.system(self.FLAC_COMM + self.FILE_PATH + '.wav')
                f = open(self.FILE_PATH + '.flac', 'rb')
                flac_cont = f.read()
                f.close()
        
                results = {}
                results['GoogleASR'] = [r.encode('ascii', 'ignore').lower() for r in self.google_asr.recognize_data(flac_cont)]
                results['NuanceASR'] = [args[1][0].lower()]
                print "[" + self.inst.__class__.__name__ + "] " + str(results)
                self.timeout = 0
                self.nuance_asr.pause(False)
                self.audio_recorder.stopMicrophonesRecording()
                self.audio_recorder.startMicrophonesRecording(self.FILE_PATH + ".wav", "wav", 16000, self.CHANNELS)
                self.memory.raiseEvent("VordRecognized", results)
        
            def text_done_callback(self, *args, **kwargs):
                if self.is_enabled:
                    if args[1] == 0:
                        self.audio_recorder.stopMicrophonesRecording()
                        self.nuance_asr.pause(True)
                    else:
                        self.audio_recorder.stopMicrophonesRecording()
                        self.audio_recorder.startMicrophonesRecording(self.FILE_PATH + ".wav", "wav", 16000, self.CHANNELS)
                        self.nuance_asr.pause(False)
        
            def enable_callback(self, *args, **kwargs):
                if args[1] == 1:
                    self.audio_recorder.stopMicrophonesRecording()
                    self.unsubscribe(SpeechRecognition.WR_EVENT)
                    self.is_enabled = False
                else:
                    self.audio_recorder.stopMicrophonesRecording()
                    self.audio_recorder.startMicrophonesRecording(self.FILE_PATH + ".wav", "wav", 16000, self.CHANNELS)
                    self.subscribe(
                        event=SpeechRecognition.WR_EVENT,
                        callback=self.word_recognized_callback
                    )
                    self.is_enabled = True
        
            def reset(self):
                if self.is_enabled:
                    print "[" + self.inst.__class__.__name__ + "] Reset recording.."
                    self.audio_recorder.stopMicrophonesRecording()
                    self.audio_recorder.startMicrophonesRecording(self.FILE_PATH + ".wav", "wav", 16000, self.CHANNELS)
        
            def _spin(self, *args):
                while not self.__shutdown_requested:
                    for f in args:
                        f()
                    time.sleep(.1)
                    self.timeout = self.timeout + 1
                    if self.timeout > 300:
                        self.timeout = 0
                        self.reset()
        
            def signal_handler(self, signal, frame):
                print "[" + self.inst.__class__.__name__ + "] Caught Ctrl+C, stopping."
                self.audio_recorder.stopMicrophonesRecording()
                self.__shutdown_requested = True
                print "[" + self.inst.__class__.__name__ + "] Good-bye"
        
        def main():
            parser = argparse.ArgumentParser()
        
            parser.add_argument("-i", "--pip", type=str, default="127.0.0.1",
                                help="Robot ip address")
            parser.add_argument("-p", "--pport", type=int, default=9559,
                                help="Robot port number")
            parser.add_argument("-l", "--lang", type=str, default="en",
                                help="Use one of the supported languages (only English at the moment)")
            parser.add_argument("--word-spotting", action="store_true",
                                help="Run in word spotting mode")
            parser.add_argument("--no-audio", action="store_true",
                                help="Turn off bip sound when recognition starts")
            parser.add_argument("--no-visual", action="store_true",
                                help="Turn off blinking eyes when recognition starts")
            parser.add_argument("-v", "--vocabulary", type=str, default="resources/nuance_grammar.txt",
                                help="A txt file containing the list of sentences composing the vocabulary")
            parser.add_argument("-k", "--keys", type=str, default="resources/google_keys.txt",
                                help="A txt file containing the list of the keys for the Google ASR")
            args = parser.parse_args()
        
            sr = SpeechRecognition(
                ip=args.pip,
                port=args.pport,
                language=args.lang,
                word_spotting=args.word_spotting,
                audio=not args.no_audio,
                visual=not args.no_visual,
                vocabulary_file=args.vocabulary,
                google_keys=args.keys
            )
            sr.update_globals(globals())
            sr.start()
        
        if __name__ == "__main__":
            main()
        ```
        </details>

    * **text_to_speech/**
      * `__init__.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown

        ```
        </details>

      * `text_to_speech.py`
        <details>
        <summary>View Content (Converted to Markdown)</summary>

        ```markdown
        import argparse
        import signal
        from naoqi import ALProxy, ALBroker, ALModule
        from event_abstract import *
        
        class TextToSpeech(EventAbstractClass):
            PATH = ''
            EVENT_NAME = "Veply"
        
            def __init__(self, ip, port, body_language_mode, speed, pitch):
                super(self.__class__, self).__init__(self, ip, port)
                self.__shutdown_requested = False
                signal.signal(signal.SIGINT, self.signal_handler)
                self.tts = ALProxy("ALTextToSpeech")
                self.tts.setParameter("speed", speed)
                self.tts.setParameter("pitchShift", pitch)
        
                #self.breathing = ALProxy("ALMotion")
                #self.breathing.setBreathEnabled('Arms', True)
                #self.configuration = {"bodyLanguageMode": body_language_mode}
        
            def start(self, *args, **kwargs):
                self.subscribe(
                    event=TextToSpeech.EVENT_NAME,
                    callback=self.callback
                )
        
                print "[" + self.inst.__class__.__name__ + "] Subscribers:", self.memory.getSubscribers(TextToSpeech.EVENT_NAME)
        
                self._spin()
        
                self.unsubscribe(TextToSpeech.EVENT_NAME)
                self.broker.shutdown()
        
            def callback(self, *args, **kwargs):
                self.tts.say(args[1])
        
                #self.tts.say(args[1], self.configuration)
        
            def _spin(self, *args):
                while not self.__shutdown_requested:
                    for f in args:
                        f()
                    time.sleep(.1)
                #self.breathing.setBreathEnabled("Arms", False)
        
            def signal_handler(self, signal, frame):
                print "[" + self.inst.__class__.__name__ + "] Caught Ctrl+C, stopping."
                self.__shutdown_requested = True
                print "[" + self.inst.__class__.__name__ + "] Good-bye"
        
        def main():
            parser = argparse.ArgumentParser()
        
            parser.add_argument("-i", "--pip", type=str, default="127.0.0.1",
                                help="Robot ip address")
            parser.add_argument("-p", "--pport", type=int, default=9559,
                                help="Robot port number")
            parser.add_argument("-l", "--language-mode", type=str, default="contextual",
                                help="The body language modality while speaking",
                                choices=['contextual', 'random', 'disabled'])
            parser.add_argument("-s", "--speed", type=int, default=90,
                                help="The speaking speed")
            parser.add_argument("-t", "--pitch", type=float, default=0.9,
                                help="The speaking pitch")
        
            args = parser.parse_args()
        
            tts = TextToSpeech(
                ip=args.pip,
                port=args.pport,
                body_language_mode=args.language_mode,
                speed=args.speed,
                pitch=args.pitch
            )
            tts.update_globals(globals())
            tts.start()
        
        if __name__ == "__main__":
            main()
        ```
        </details>

  * **sonar/**
    * `getsonar.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/core/almemory-api.html
      #http://doc.aldebaran.com/2-5/family/pepper_technical/pepper_dcm/actuator_sensor_names.html#ju-sonars
      
      import qi
      import argparse
      import sys
      import time
      import threading
      
      sonarValueList = ["Device/SubDeviceList/Platform/Front/Sonar/Sensor/Value",
                        "Device/SubDeviceList/Platform/Back/Sonar/Sensor/Value"]
      
      import threading
      import os
      
      def rhMonitorThread (memory_service):
          t = threading.currentThread()
          while getattr(t, "do_run", True):
              sonarValues =  memory_service.getListData(sonarValueList)
              print "[Front, Back]", sonarValues
              time.sleep(1)
          print "Exiting Thread"
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["SonarReader", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          memory_service  = session.service("ALMemory")
      
          #create a thead that monitors directly the signal
          monitorThread = threading.Thread(target = rhMonitorThread, args = (memory_service,))
          monitorThread.start()
      
          #Program stays at this point until we stop it
          app.run()
      
          monitorThread.do_run = False
      
          print "Finished"
      
      if __name__ == "__main__":
          main()
      ```
      </details>

    * `sonar_sim.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      # Sonar simulation using memory keys
      #
      # Device/SubDeviceList/Platform/Front/Sonar/Sensor/Value
      # Device/SubDeviceList/Platform/Back/Sonar/Sensor/Value
      
      import qi
      import argparse
      import sys
      import time
      import threading
      import os
      
      memkey = {
          'SonarFront': 'Device/SubDeviceList/Platform/Front/Sonar/Sensor/Value',
          'SonarBack':  'Device/SubDeviceList/Platform/Back/Sonar/Sensor/Value' }
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--sensor", type=str, default="SonarFront",
                              help="Sensor: SonarFront, SonarBack")
          parser.add_argument("--value", type=float, default=0.75,
                              help="Sensor measurement")
          parser.add_argument("--duration", type=float, default=3.0,
                              help="Duration of the event")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["SonarSim", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          memory_service  = session.service("ALMemory")
      
          val = None
          try:
              val = float(args.value)
          except:
              print("ERROR: value not numerical")
              return
      
          try:
              mkey = memkey[args.sensor]
              print("Sonar %s = %f" %(args.sensor,val))
              memory_service.insertData(mkey,val)
              time.sleep(args.duration)
              memory_service.insertData(mkey,0.0)
          except:
              print("ERROR: Sensor %s unknown" %args.sensor)
      
      if __name__ == "__main__":
          main()
      ```
      </details>

  * **tablet/**
    * `play_video.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/core/altabletservice-api.html
      
      import qi
      import argparse
      import sys
      import time
      import os
      
      run = False
      
      def onVideoFinished():
          global run
          print "Video play finished."
          run = False
      
      def main():
          global run
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address. On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--url", type=str, default="video.mp4",
                              help="video to play (mp4 H264/AAC)")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          weburl = args.url
      
          #Start working session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          tablet_service = session.service("ALTabletService")
      
          if weburl.startswith('http'):
              strurl=weburl
          else:
              strurl = "http://198.18.0.1/apps/spqrel/%s" %(weburl)
          print "URL: ",strurl
      
          #strurl = weburl
          tablet_service.playVideo(strurl) # non blocking
      
          run = True
      
          idVF = tablet_service.videoFinished.connect(onVideoFinished)
          #app.start()
      
          while (run):
              time.sleep(0.5)
      
          tablet_service.videoFinished.disconnect(idVF)
          #app.stop()
      
      if __name__ == "__main__":
          main()
      
      ```
      </details>

    * `show_image.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/core/altabletservice-api.html
      
      import qi
      import argparse
      import sys
      import time
      import os
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address. On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--image", type=str, default="default.jpg",
                              help="Image to show (in spqrel_apps/html/ folder)")
          parser.add_argument("--folder", type=str, default=None,
                              help="Folder with images to show")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          imfile = args.image
          folder = args.folder
      
          #Start working session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          tablet_service = session.service("ALTabletService")
      
          if folder==None:
      
              # Display a local image located in img folder in the root of the web server
              # The ip of the robot from the tablet is 198.18.0.1
              imgurl = "http://198.18.0.1/apps/spqrel/%s" %(imfile)
              print imgurl
              tablet_service.showImage(imgurl)
      
              # tablet_service.hideImage()
      
          else:
      
              n = 6
              for i in range(0,n):
                  imgurl = "http://198.18.0.1/apps/spqrel/%s/%03d.jpg" %(folder,i)
                  print imgurl
                  tablet_service.showImage(imgurl)
                  time.sleep(2)
      
      if __name__ == "__main__":
          main()
      
      ```
      </details>

    * `show_web.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/core/altabletservice-api.html
      
      import qi
      import argparse
      import sys
      import time
      import os
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address. On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--url", type=str, default="index.html",
                              help="web page/URL to show (in spqrel_apps/html/ folder)")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          weburl = args.url
      
          #Start working session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          tablet_service = session.service("ALTabletService")
      
          # Display a local image located in img folder in the root of the web server
          # The ip of the robot from the tablet is 198.18.0.1
      
          if weburl.startswith('http'):
              strurl=weburl
          else:
              strurl = "http://198.18.0.1/apps/spqrel/%s" %(weburl)
          print "URL: ",strurl
          tablet_service.showWebview(strurl)
      
          #time.sleep(10)
      
          # Hide the web view
          # tablet_service.hideImage()
      
      if __name__ == "__main__":
          main()
      
      ```
      </details>

    * `touchscreen.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/core/altabletservice-api.html
      
      import qi
      import argparse
      import sys
      import os
      
      # function called when the signal onTouchDown is triggered
      def onTouched(x, y):
          print "coordinates are x: ", x, " y: ", y
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["TabletModule", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          tablet_service = session.service("ALTabletService")
      
          idTTouch = tablet_service.onTouchDown.connect(onTouched)
          app.run()
      
      if __name__ == "__main__":
          main()
      ```
      </details>

  * **tf/**
    * `getPosition.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #! /usr/bin/env python
      
      #http://doc.aldebaran.com/2-5/naoqi/motion/control-cartesian-api.html
      """Example: Use getPosition Method"""
      
      import qi
      import argparse
      import sys
      import motion
      import os
      
      frames = ["Torso", "World", "Robot"]
      
      def main(session, sensor, frame):
          """
          This example uses the getPosition method.
          """
          # Get the service ALMotion.
      
          motion_service  = session.service("ALMotion")
      
          # Example showing how to get the position of the top camera
          name            = sensor
          useSensorValues = True
          result          = motion_service.getPosition(name, frame, useSensorValues)
      
          print "Position of", name, " in frame", frames[frame] , " is:"
          print "Translation (x,y,z) = [", result[0],",", result[1],",", result[2],"]"
          print "Rotation    (x,y,z) = [", result[3],",", result[4],",", result[5],"]"
      
          print "Transformation: "
          result = motion_service.getTransform(name, frame, useSensorValues)
          for i in range(0, 4):
              for j in range(0, 4):
                  print result[4*i + j],
              print ''
      
      if __name__ == "__main__":
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--sensor", type=str, default="CameraTop",
                              help="Sensor name. To check possible names use getSensorNames.py.")
          parser.add_argument("--frame", type=int, default=2,
                              help="Frame with respect to which express position: (0 = Torso, 1 = World, 2 = Robot)")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
          sensor = args.sensor
          frame = args.frame
      
          #Start working session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          main(session, sensor, frame)
      ```
      </details>

    * `getSensorNames.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #! /usr/bin/env python
      
      #http://doc.aldebaran.com/2-5/naoqi/motion/tools-general-api.html
      
      """Example: Use getSensorNames Method"""
      
      import qi
      import argparse
      import sys
      import os
      
      def main(session):
          """
          This example uses the getSensorNames method.
          """
          # Get the service ALMotion.
      
          motion_service  = session.service("ALMotion")
      
          # Example showing how to get the list of the sensors
          sensorList = motion_service.getSensorNames()
          for sensor in sensorList:
              print sensor
      
      if __name__ == "__main__":
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Start working session
          session = qi.Session()
          try:
              session.connect("tcp://" + pip + ":" + str(pport))
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          main(session)
      ```
      </details>

  * **touch/**
    * `react_touch.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      #http://doc.aldebaran.com/2-5/naoqi/core/almemory-api.html
      #http://doc.aldebaran.com/2-5/naoqi/sensors/altouch-api.html
      #http://doc.aldebaran.com/2-5/dev/libqi/api/python/signal.html
      #http://doc.aldebaran.com/2-5/family/pepper_technical/pepper_dcm/actuator_sensor_names.html
      
      import qi
      import argparse
      import sys
      import time
      import threading
      import os
      
      def check_event(touch_service):
          larm = False
          rarm = False
          s = touch_service.getStatus()
          for e in s:
              if e[0]=='LArm' and e[1]:
                  larm = True
              if e[0]=='RArm' and e[1]:
                  rarm = True
          return larm and rarm
      
      def rhMonitorThread (memory_service, touch_service):
          rhMemoryDataValue = "Device/SubDeviceList/RHand/Touch/Back/Sensor/Value"
          t = threading.currentThread()
          while getattr(t, "do_run", True):
              print "Right Hand value thread=", memory_service.getData(rhMemoryDataValue)
              b = check_event(touch_service)
              print "Two hands touched: ",b
              time.sleep(1)
          print "Exiting Thread"
      
      touchstatus = { }
      
      def onTouched(value):
          global touchstatus
          print "Touch value=",value
      
          touched_bodies = []
          for p in value:
              if p[1]:
                  touched_bodies.append(p[0])
              touchstatus[p[0]] = p[1]
      
          print touched_bodies
          print 'Status: ', touchstatus
      
      def rhTouched(value):
          print "Right Hand value subscriber=",value
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["ReactToTouch", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          memory_service  = session.service("ALMemory")
      
          #Testing some functions from the ALTouch module
          touch_service = session.service("ALTouch")
      
          touchsensorlist = touch_service.getSensorList() # vector of sensor names
          print touchsensorlist
          # ['Head/Touch/Front', 'Head/Touch/Middle', 'Head/Touch/Rear', 'LHand/Touch/Back', 'RHand/Touch/Back', 'Bumper/Back', 'Bumper/FrontLeft', 'Bumper/FrontRight']
      
          touchstatus = touch_service.getStatus()  # vector of [name, bool, []]
          print touchstatus
          #[['Head', False, []], ['LArm', False, []], ['Leg', False, []], ['RArm', False, []], ['LHand', False, []], ['RHand', False, []], ['Bumper/Back', False, []], ['Bumper/FrontLeft', False, []], ['Bumper/FrontRight', False, []], ['Head/Touch/Front', False, []], ['Head/Touch/Middle', False, []], ['Head/Touch/Rear', False, []], ['LHand/Touch/Back', False, []], ['RHand/Touch/Back', False, []], ['Base', False, []]]
      
          #subscribe to any change on any touch sensor
          anyTouch = memory_service.subscriber("TouchChanged")
          idAnyTouch = anyTouch.signal.connect(onTouched)
      
          #subscribe to any change on "HandRightBack" touch sensor
          rhTouch = memory_service.subscriber("HandRightBackTouched")
          idRHTouch = rhTouch.signal.connect(rhTouched)
      
          #create a thead that monitors directly the signal
          monitorThread = threading.Thread(target = rhMonitorThread, args = (memory_service,touch_service))
          monitorThread.start()
      
          #Program stays at this point until we stop it
          app.run()
      
          #Disconnecting callbacks and Threads
          anyTouch.signal.disconnect(idAnyTouch)
          rhTouch.signal.disconnect(idRHTouch)
          monitorThread.do_run = False
      
          print "Finished"
      
      if __name__ == "__main__":
          main()
      ```
      </details>

    * `touch_sim.py`
      <details>
      <summary>View Content (Converted to Markdown)</summary>

      ```markdown
      # Touch simulation using memory keys
      #
      # Device/SubDeviceList/Head/Touch/Middle/Sensor/Value
      # Device/SubDeviceList/LHand/Touch/Back/Sensor/Value
      # Device/SubDeviceList/RHand/Touch/Back/Sensor/Value
      
      import qi
      import argparse
      import sys
      import time
      import threading
      import os
      
      memkey = {
          'HeadMiddle': 'Device/SubDeviceList/Head/Touch/Middle/Sensor/Value' ,
          'LHand':      'Device/SubDeviceList/LHand/Touch/Back/Sensor/Value' ,
          'RHand':      'Device/SubDeviceList/RHand/Touch/Back/Sensor/Value' }
      
      def main():
          parser = argparse.ArgumentParser()
          parser.add_argument("--pip", type=str, default=os.environ['PEPPER_IP'],
                              help="Robot IP address.  On robot or Local Naoqi: use '127.0.0.1'.")
          parser.add_argument("--pport", type=int, default=9559,
                              help="Naoqi port number")
          parser.add_argument("--sensor", type=str, default="HeadMiddle",
                              help="Sensor: HeadMiddle, LHand, RHand")
          parser.add_argument("--duration", type=float, default=3.0,
                              help="Duration of the event")
      
          args = parser.parse_args()
          pip = args.pip
          pport = args.pport
      
          #Starting application
          try:
              connection_url = "tcp://" + pip + ":" + str(pport)
              app = qi.Application(["TouchSim", "--qi-url=" + connection_url ])
          except RuntimeError:
              print ("Can't connect to Naoqi at ip \"" + pip + "\" on port " + str(pport) +".\n"
                     "Please check your script arguments. Run with -h option for help.")
              sys.exit(1)
      
          app.start()
          session = app.session
      
          #Starting services
          memory_service  = session.service("ALMemory")
      
          try:
              mkey = memkey[args.sensor]
              print("Touching %s ..." %args.sensor)
              memory_service.insertData(mkey,1.0)
              time.sleep(args.duration)
              memory_service.insertData(mkey,0.0)
              print("Touching %s ... done" %args.sensor)
          except:
              print("ERROR: Sensor %s unknown" %args.sensor)
      
      if __name__ == "__main__":
          main()
      ```
      </details>

