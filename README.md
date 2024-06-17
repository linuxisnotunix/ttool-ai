# Detailed Instructions for Installing Our Framework, Replicating Our Results, and Using It on Other Models

## 1. Installing TTool(-AI)
First, you need to install TTool. You will find detailed instructions [here](https://ttool.telecom-paris.fr/installation.html). We recommend downloading and compiling it from the source code available on its GitLab repository:
```bash
git clone https://gitlab.telecom-paris.fr/mbe-tools/TTool.git
cd TTool
make ttool-cli && make ttoolnotest
make install
```

## 2. Configuring TTool-AI
You will need an OpenAI API key.
- Open the TTool configuration file: `./bin/config.xml`.
- Enter your key by adding this line in the configuration file: `<OPENAIKey data="put_your_key_here" />`.
- Add the following line to enable the different available GPT models: `<OPENAIModel data="gpt-4o gpt4-turbo gpt-3.5-turbo" />`.


## 3. Replicating Our Results

### 3.1. Opening the Models
- Launch TTool:
```bash
./ttool.exe
```
- Click on `File > Open Model` and browse your local copy of the Zenodo repository, `incoherencies` directory, and choose a model, e.g., `spacebasedsystem.xml`. You will see several tabs: `BD1` and `BD2` correspond to the two input block diagrams, and in the `UCD` tab, the subtabs `UCD1` and `UCD2` correspond to the two input sequence diagrams. The other tabs correspond to the models corrected by the framework: `BDx_UCDy` contains the block diagram x modified by taking into account the inconsistencies detected by comparing it with the use-case diagram y.

### 3.2. Replicating the Inconsistencies Detection
Select a block diagram (BD1 or BD2), make a right click, "to textual format". Do the same for UCD1 or UCD2.

Now, open the AI window (use the icon on the right depicting a brain), then select "Identify incoherencies". In the question box, put:
- specification: the specification of the system
- Diagram1: Use case diagram. There, paste the textual format of the UCD you have selected before
- Diagram2: Block diagram. There, paste the textual format of the BD you have selected just before

The question box should now contain the system specification, then the textual description of the UCD, then the textual description of the BD. Now, click on "start". If TTool was able to exchange with ChatGPT, then you should obtain a list of incoherencies, that could look like this:

```
AI: json
{
  "incoherencies": [
    {
      "diagram": "Diagram1",
      "description": "There is no actor or use case for ErrorCorrectionCode, which is a significant part of the system as per the specification."
    },
    {
      "diagram": "Diagram1",
      "description": "Use case 'Monitor_Safety_Data' is shown to be associated with RF_Receiver, which is incorrect as monitoring is a ground station activity."
    },
    {
      "diagram": "Diagram2",
      "description": "Block 'GroundStation' has attributes for temperature, batteryLevel, and fuelQuantity, which are not its attributes but those of the space-based system."
    },
    {
      "diagram": "Diagram2",
      "description": "Signal 'tM_7978(int value)' in RFTransmitter and its corresponding relation are unaccounted for in Diagram1."
    },
    {
      "diagram": "Diagram2",
      "description": "Signal 'memoryWriteCRC()' and 'memoryReadCRC()' in 'ErrorCorrectionCode' block have no corresponding use cases or activities in Diagram1."
    },
    {
      "diagram": "Diagram2",
      "description": "There is no block representing the Watchdog's activity of checking if tasks are still responsive, which is a critical system function as per the specification."
    },
    {
      "diagram": "Diagram2",
      "description": "There is no representation for the cancellation and recomputation of TM upon receiving another TC, as described in the specification."
    },
    {
      "diagram": "Diagram2",
      "description": "The association of 'Microcontroller' with the task of restarting the system if tasks are unresponsive is not clearly translated into relationships with other blocks."
    }
  ]
}
```
Note: reproducing this exact list of incoherencies is not possible since ChatGPT introduces randomness among the most probable results.


### 3.3. Updating diagrams from incoherencies

Imagine that you now wish to update the block diagram considering these inconsistencies. Open the AI window of TTool. Select at the top "system blocks". In the question field, put:
- "Specification:" followed with the system specification
- "Diagram1, use case diagram:" followed with the textual description of your selected UCD
- "Diagram2, block diagram:" followed with the textual description of your selected BD
- the following text, taken from incoherencies:
```
Do correct the block diagram considering the following incoherencies:
1. Block 'GroundStation' has attributes for temperature, batteryLevel, and fuelQuantity, which are not its attributes but those of the space-based system
2. Signal 'tM_7978(int value)' in RFTransmitter and its corresponding relation are unaccounted for in Diagram1
3. There is no block representing the Watchdog's activity of checking if tasks are still responsive, which is a critical system function as per the specification
4. There is no representation for the cancellation and recomputation of TM upon receiving another TC, as described in the specification
5. The association of 'Microcontroller' with the task of restarting the system if tasks are unresponsive is not clearly translated into relationships with other blocks
Do correct incoherency 1-5 to propose a new block diagram.

```
Click on start, wait. Then click on apply, and reorganize the blocks ot improve their graphical representation: you should have a readable updated Block Diagram.


### 3.4. Trying our framework on your own models
You can also test our framework using diagrams you have created yourself with TTool. If you want to generate these diagrams using TTool-AI, follow these steps:
1. Copy and paste the desired specification into the TTool-AI window.
2. Select either `Identify use cases` or `Identify system blocks`.
3. Click on `Start`.
4. Once the LLM has responded, click on `Apply response`.

And now, follow the steps explained in 4.2 and 4.3 for detecting and correcting inconsistencies.

Enjoy!
