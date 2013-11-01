// Register with the server
imp.configure("Morse Example", [], []);

// morsecode
code <- {
	a = "._",    b = "_...",  c = "_._.",  d = "_..",   e = ".",     f = ".._.",
	g = "__.",   h = "....",  i = "..",    j = ".___",  k = "_._",   l = "._..",
	m = "__",    n = "_.",    o = "___",   p = ".__.",  q = "__._",  r = "._.",
	s = "...",   t = "_",     u = ".._",   v = "..._",  w = ".__",   x = "_.._",
	y = "_.__",  z = "__.."
};

local factor = 3.0;

local startTime = 0.0;
local endTime;
local recordedWords = "";
local recordedLetter = "";

function mapLetter(letter) {
    foreach (index,val in code){
        if (val == letter) {
            recordedWords += index;
            break;
        }
    }
}
function lightBlue () {
    if (hardware.pin1.read() == 0)
        hardware.pin7.write(1);
}
function lightRed () {
    if (hardware.pin1.read() == 1)
        hardware.pin8.write(1);
}
function resetLights () {
    hardware.pin5.write(0);
    hardware.pin7.write(0);
    hardware.pin8.write(0);
}
// readButton function called every 100ms
function changeState()
{
    resetLights();
    endTime = hardware.millis();
    local pinState = hardware.pin1.read();
    hardware.pin5.write(1-pinState);
    local diffTime = endTime - startTime;
    if (1-pinState){
        imp.wakeup(3/factor, lightBlue);
        if(diffTime >= 7000/factor){
            mapLetter(recordedLetter);
            recordedWords += " ";
            recordedLetter = "";
        } else if (diffTime >= 3000/factor) {
            mapLetter(recordedLetter);
            recordedLetter = "";
        }
    } else if (pinState){
        if (diffTime > 3000/factor){
            recordedLetter += "_";
        } else {
            recordedLetter += ".";
        }
    }
    imp.wakeup(3/factor, lightRed);
    startTime = hardware.millis();
}
function parseWord(words){
    words = words.tolower();
    for (local i = 0; i < words.len() ; i+=1) {
        if (words.slice(i, i+1) == " "){
            imp.sleep(7/factor);
        } else {
            // Sorts through each letter
            foreach(index,val in code){
                if( index == words.slice(i, i+1)){
                    // Lights up each piece of Morse
                    foreach(j,c in val){
                        // Checks dit
                        if(c == 46){
                            hardware.pin5.write(1);
                            imp.sleep(1/factor);
                        }
                        // Checks dah
                        if(c == 95){
                            hardware.pin5.write(1);
                            imp.sleep(3/factor);
                        }
                        hardware.pin5.write(0);
                        imp.sleep(1/factor);
                    }
                    break;   
                }
            }
            imp.sleep(3/factor);
        }
    }
}
function sendMessage(){
    if (hardware.pin2.read() == 1){
        mapLetter(recordedLetter);
        if (recordedWords == ""){
            agent.send("log", "Message was Empty");
        } else {
            agent.send("msg", recordedWords);
            recordedLetter = "";
            recordedWords = "";
        }
    }
    resetLights();
}
 
hardware.pin1.configure(DIGITAL_IN_PULLUP, changeState);
hardware.pin2.configure(DIGITAL_IN_PULLUP, sendMessage);
hardware.pin5.configure(DIGITAL_OUT);
hardware.pin7.configure(DIGITAL_OUT);
hardware.pin8.configure(DIGITAL_OUT);
agent.on("incoming", parseWord );
