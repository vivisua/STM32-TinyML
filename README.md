# STM32 TinyML Project

## Envionments
 * STM32CubeMX(https://www.st.com/en/development-tools/stm32cubemx.html)
 * MDK5-ARM(https://www2.keil.com/mdk5)
 * ST-Link Driver(https://www.st.com/content/my_st_com/en/products/development-tools/software-development-tools/stm32-software-development-tools/stm32-utilities/stsw-link009.license=1656325086116.product=STSW-LINK009.version=2.0.2.html)
 * STM32 Board (e.g. NUCLEO-L432KC)
 * Containing the structure and parameters of the learning model ***.onnx file*** (if trained by Pytorch)
 * Download ***validation.py*** , ***framework_x-cube-ai folder*** and then prepare a ***dataset*** for verification in one folder.

## How to deploy training model?
 * **STM32CubeMX**  

  1. Open STM32CubeMX and click ***ACCESS TO BOARD SELECTER*** and select our board.

     ![mainmenu](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/mainmenu.png)

     ![select board](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/select%20board.png)

  2. Click ***Software Packs --- Manage Software Packs*** and 

     ![manage software packs](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/manage%20software%20packs.png)

     Click the check box of <u>Artifacial Intelligence( v7.1.0 or Upper version )</u> under **X.CUBE.AI** and click ***Install Now.***

     ![install](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/install.png)

  3. Click ***Software Packs --- Select Components*** and set option as follow.

     ![components](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/components.png)

     <img src="https://raw.githubusercontent.com/AugustZTR/picbed/master/img/image-20220826184229102.png" alt="image-20220826184229102" style="zoom:50%;" />

  4. The `TIM` must be enabled to perform accurate inference latency measurement: 
     ![image-20220917144705663](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/image-20220917144705663.png)

     Make sure the Parameter Settings are modified as the figure shown below:

     ![image-20220917144834834](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/image-20220917144834834.png)

  5. Set AI network

     First, set the communication options as follow

     ![plantform setting](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/plantform%20setting.png)

     Then click ***Add network*** and import ours model by follow steps shown in the picture.

     ![image-20220627201730152](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/image-20220627201730152.png)

     After choosing model, you can click ***Analyze*** to view the resources needed to run the model.

     ![image-20220627202020441](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/image-20220627202020441.png)

  6. Set Clock

     ![clock](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/clock.png)

  7. When generating code, make sure to select `Basic` option in `Application Structure`. 

     <img src="https://raw.githubusercontent.com/AugustZTR/picbed/master/img/image-20220915101630837.png" alt="image-20220826184853048" style="zoom:50%;" />

     `TIM` in LL library should be selected in `Project Manager-Advance Settings`.

     ![image-20220917145024951](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/image-20220917145024951.png)

     In order to operate the header file later, don't forget to check option `Generate peripheral initialization as a pair of '.c/.h' files per peripheral` in the Code Generator when generating code.

     <img src="https://raw.githubusercontent.com/AugustZTR/picbed/master/img/image-20220915093242012.png" alt="image-20220915093242012" style="zoom:67%;" />
     
  8. Finish settings and click ***GENERATE CODE***

     ![project manager](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/project%20manager.png)
     
     * ****
     * **The stack and heap sizes of the original Keil project were set as just 0x800 (2,048 bytes each), which caused unknown bugging behaviours in running.**
     * **After setting stack and heap sizes as 0x3000 and 0x5000, our code was safely run.**  

  ## Load Program to Board
  
  1. After generating C source code based project, you should replace certain files in order to enable evaluation. All four files that should be replaced are provided. You could directly copy and paste the files from the folder `framework_x-cube-ai` to the generated project in Keil:

     1. **usart.c**
     2. **app_x-cube-ai.c**
     3. **app_x-cube-ai.h**
     4. **main.c**

  2. When build the project, make sure that you check `Use MicroLIB` in `Setting-Target-Code Generation`. 

     <img src="https://raw.githubusercontent.com/AugustZTR/picbed/master/img/image-20220916085712870.png" alt="image-20220915093242012" style="zoom:67%;" />

  3. Connet the boadr to computer.

     <img src="https://raw.githubusercontent.com/AugustZTR/picbed/master/img/image-20220627203515997.png" alt="image-20220627203515997" style="zoom: 25%;" />

  4. Open project in MDK5 and build.

     ![build](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/build.png)
     
     * **If an error like <L6050U: The code size of this image exceeds the maximum allowed for this version...> occur, your model size should be reduced. The Lite version can only be compiled up to 32 Kb.**
     * **One way is to reduce the size by changing Optimization from C/C++ (Tap) to -Oz image size or -Os balanced in the Option. (But it's not going to be much.)**

  5. Check if the debugger is connected.

     First, click ***Options for Target***.

     ![targets](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/targets.png)

     Then switch to <u>Debug</u> and click ***Settings***.

     <img src="https://raw.githubusercontent.com/AugustZTR/picbed/master/img/debug.png" alt="debug"  />

     If the debugger is connected, you can see the IDCODE and the Device Name. 

     <img src="https://raw.githubusercontent.com/AugustZTR/picbed/master/img/swdio.png" alt="swdio"  />

     Finally, switch to <u>Flash Download</u> and check <u>Reset and Run</u>(option)

     ![full chip](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/full%20chip.png)

  6. Now you can load program to the board.

     ![load](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/load.png)
     
     * **If you press the Debug tap, Build button and Keil Tool is forced to exit, proceed to the next step.**
 
 ## Deploy though STM32CubeProgrammer
  1. Install STM32CubeProgrammer (https://www.st.com/en/development-tools/stm32cubeprog.html)
    <img src="img/stm32prog.png">
     
   * **If you do not see the STM32CubeProgrammer icon after the installation is complete, try installing a different version.**
    
  2. When the installation is complete, please run STM32CubeProgrammer.
    <img src="img/prog_window.png">
    
  3. Click open file Tap next to Device memory Tap
    
  4. If you have successfully completed the Build process, the .hex file will exist in the ***ST\"Project Name"\MDK-ARM\"Project Name"\ directory***.
    <img src="img/hex_selec.png">
    * **The project name I created is Test. It could be different from me.**
    
  5. Press the Connect button on the right and then the Download button.
    <img src="img/co_do.png">
    
  6. This process allows you to deploy models on the board without the Keil Tool.
    

 ## Validation in python
  1. First, go to Device Manager in Windows.
     * On the Ports tab, check the number of COM ports the device is connected.
     * And go to the Port Settings tab and change Bit/S(B) to 115200.
     <img src="img/Device_manager.png">
     
  2. Then, you go down to the bottom of the validation.py file, you will see argparser codes like above. Here, '--com', '--path_data' must be modified.
     * com is the name of the port to which the board is connected, and path_data is the location of the data set that was used when learning.
     <img src="img/argparser.png">
     * **(The verification is conducted in the Windows environment, so you must download dataset to Windows and enter the location in default='path' path.)**
     
  3. You must install the required Python library before running.(serial, numpy, scikit-learn, tqdm, pyserial)
     * "pip install serial numpy scikit-learn tqdm pyserial"
     * <img src="img/pip_install.png">
     * **If an error such as "ERROR: Error [WinError 225]" occurred during installation, shut down the Windows Defender and all vaccines.**
     
  4. When you finish installing the library, you can run it through "python validation.py" 
     * <img src="img/validation.png">
  
  5. Finally, Click the reset button on the bottom of the board and start validation process.
     ![image-20220627203955477](https://raw.githubusercontent.com/AugustZTR/picbed/master/img/image-20220627203955477.png)
    

  6. After verification, you can check the results.
     
     * <img src="img/validation_result.png">
