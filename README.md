## unmaintained

Sorry but I cannot take time to maintain the library now...

## web3-arduino

![img](https://user-images.githubusercontent.com/891384/36104056-4994f600-1054-11e8-94f4-9f067610a6bf.png)

- What is this library?
    - This is an Arduino (or ESP32) library to use web3 on Ethereum platform.

- What is Arduino? 
    - Arduino is an open source computer hardware and software.
    - https://www.arduino.cc/
- What is ESP32?
    - ESP32 is a series of low cost, low power system on a chip microcontrollers with integrated Wi-Fi and dual-mode Bluetooth. 
    - https://www.espressif.com/en/products/hardware/esp32/overview    
- What is web3?
    - Web3 is the Ethereum compatible API which implements the Generic JSON RPC spec. Originally Javascript version is developed.
    - https://github.com/ethereum/web3.js/
- What is Ethereum?
    - Ethereum is a decentralized platform for applications that run exactly as programmed without any chance of fraud, censorship or third-party interference.
    - https://www.ethereum.org/
    
## Environment

- Confirmed device
    - ESP-WROOM-32
- Used Ethereum client
    - INFURA (https://infura.io)

## Installation

1. download the zip file from `Clone or download` button on Github.
2. launch Arduino IDE.
3. `Sketch` -> `Include Library` -> `Add .ZIP file` -> select downloaded zip file.
4. Then you can use this from Arduino IDE.

## Example

The following example sends a contract call on the Kovan testnet to set a value in a variable.

### Contract
```c++
pragma solidity ^0.4.18;

contract Sample {
    uint data;

    function set(uint d) public{
        data = d;
    }

    function get() public constant returns (uint retVal) {
        return data;
    }
}
```

### Code
```c++
#include <WiFi.h>
#include <Contract.h>

#include "esp_log.h"

static const char* TAG = "MyModule";

#define USE_SERIAL Serial

#define ENV_SSID     "SSID_NAME"
#define ENV_WIFI_KEY "WIFI_PASSWORD"

// Replace with your wallet address
string* MY_ADDRESS = new string("0x0000000000000000000000000000000000000000");
// Replace with your infura project id
string* INFURA_PATH = new string("/v3/YOUR_INFURA_PROJECT_ID");

string* CONTRACT_ADDRESS = new string("0xa3de8b4dd8082d1ab09ff72e91e5561e9fdef860");
string* INFURA_HOST = new string("kovan.infura.io");

// Replace with your private key
const char PRIVATE_KEY[] = {
  0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
  0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
  0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
  0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00
  };

Web3 web3(INFURA_HOST, INFURA_PATH);

void eth_send_example();

void setup() {
    USE_SERIAL.begin(115200);

    for(uint8_t t = 4; t > 0; t--) {
        USE_SERIAL.printf("[SETUP] WAIT %d...\n", t);
        USE_SERIAL.flush();
        delay(1000);
    }

    WiFi.begin(ENV_SSID, ENV_WIFI_KEY);

    // attempt to connect to Wifi network:
    while (WiFi.status() != WL_CONNECTED) {
        Serial.print(".");
        // wait 1 second for re-trying
        delay(1000);
    }

    USE_SERIAL.println("Connected");

    esp_log_level_set(TAG, ESP_LOG_VERBOSE);

    eth_send_example();
}

void loop() {
    // put your main code here, to run repeatedly:
    delay(1000);
}

void eth_send_example() {
    ESP_LOGI(TAG, "Creating the contract: %s", CONTRACT_ADDRESS);
    Contract contract(&web3, CONTRACT_ADDRESS);
    
    ESP_LOGI(TAG, "Setting the signer account private key");
    contract.SetPrivateKey((uint8_t*)PRIVATE_KEY);

    ESP_LOGI(TAG, "Getting the nonce for the account");
    uint32_t nonceVal = (uint32_t)web3.EthGetTransactionCount(MY_ADDRESS);
   
    ESP_LOGI(TAG, "Nonce = %d", nonceVal);

    uint64_t gasPriceVal = 20000000000;
    uint32_t  gasLimitVal = 3000000;

    string* valueStr = new string("0x00");
    uint8_t dataStr[100];
    memset(dataStr, 0, 100);
    string func = "set(uint256)";

    string p = contract.SetupContractData(&func, 123);

    ESP_LOGI(TAG, "Contract execution...");
    string result = contract.SendTransaction(nonceVal, gasPriceVal, gasLimitVal, CONTRACT_ADDRESS, valueStr, &p);

    ESP_LOGI(TAG, "Result = %s", result);

}
```

## Dependencies

- [cJSON](https://github.com/DaveGamble/cJSON)
- [secp256k1](https://github.com/bitcoin-core/secp256k1)
- [ESP32-Arduino](https://github.com/espressif/arduino-esp32)

