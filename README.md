## antenna-arduino

- What is this library?
    - This is an Arduino (or ESP32) library to interact with the IoTeX Blockchain.

- What is Arduino? 
    - Arduino is an open source computer hardware and software.
    - https://www.arduino.cc/
- What is ESP32?
    - ESP32 is a series of low cost, low power system on a chip microcontrollers with integrated Wi-Fi and dual-mode Bluetooth. 
    - https://www.espressif.com/en/products/hardware/esp32/overview    
- What is iotex-antenna?
    - iotex-antenna is the IoTeX compatible API which implements the Generic JSON RPC spec. Originally Javascript version is developed.
    - https://github.com/iotexproject/iotex-anetnna/
- What is IoTeX?
    - IoTeX is a blockchain platform tailor fit fot IoT. The foundation layer for "The Internet of Trusted Things".
    - https://www.iotex.io/
    
## Environment

- Confirmed device
    - ESP32-S2
- Used IoTeX client
    - ...

## Installation

1. download the zip file from `Clone or download` button on Github.
2. launch Arduino IDE.
3. `Sketch` -> `Include Library` -> `Add .ZIP file` -> select downloaded zip file.
4. Then you can use this from Arduino IDE.

## Example

Please refer `examples` directory.

### setup

```C++
#define IOTEX_CLIENT_HOST "rinkeby.infura.io"
#define IOTEX_CLIENT_PATH "/<YOUR_IOTEX_CLIENT_ID>"

Antenna antenna(IOTEX_CLIENT_HOST, IOTEX_CLIENT_PATH);
```

### call antenna methods

```C++
char result[128];

antenna.AntennaClientVersion(result);
USE_SERIAL.println(result);

antenna.AntennaSha3("0x68656c6c6f20776f726c64", result);
USE_SERIAL.println(result);
```

### `call` to Contract

```C++
Contract contract(&antenna, CONTRACT_ADDRESS);
strcpy(contract.options.from, MY_ADDRESS);
strcpy(contract.options.gasPrice,"2000000000000");
contract.options.gas = 5000000;
contract.SetupContractData(result, "get()");
contract.Call(result);
USE_SERIAL.println(result);
```

### `sendTransaction` to Contract

```C++
Contract contract(&antenna, CONTRACT_ADDRESS);
contract.SetPrivateKey((uint8_t*)PRIVATE_KEY);
uint32_t nonceVal = (uint32_t)antenna.EthGetTransactionCount((char *)MY_ADDRESS);

uint32_t gasPriceVal = 141006540;
uint32_t  gasLimitVal = 3000000;
uint8_t toStr[] = CONTRACT_ADDRESS;
uint8_t valueStr[] = "0x00";
uint8_t dataStr[100];
memset(dataStr, 0, 100);
contract.SetupContractData((char*)dataStr, "set(uint256)", 123);
contract.SendTransaction((uint8_t *) result,
                         nonceVal, gasPriceVal, gasLimitVal, toStr, valueStr, dataStr);

USE_SERIAL.println(result);
```

## Dependency

- [cJSON](https://github.com/DaveGamble/cJSON)
- [secp256k1](https://github.com/bitcoin-core/secp256k1)
- [ESP32-Arduino](https://github.com/espressif/arduino-esp32)

