### TLS 1.0:

-1999년에 발표되었습니다.
- SSL 3.0을 기반으로 하며, 안전한 인터넷 통신을 위한 프로토콜 중 하나입니다.
- RC4, DES 및 3DES 등의 대칭키 암호화 방식을 지원합니다.
- CBC (Cipher Block Chaining) 모드에서 암호화를 수행합니다.
- BEAST (Browser Exploit Against SSL/TLS)와 같은 취약점이 발견되어 보안상 취약합니다.

### TLS 1.1:

- 2006년에 발표되었습니다.
- TLS 1.0에서 발견된 취약점을 보완하고, 보안성을 향상시켰습니다.
- RC4를 사용하지 않고, AES (Advanced Encryption Standard) 암호화 방식을 지원합니다.
- TLS 1.0과 마찬가지로 CBC 모드에서 암호화를 수행합니다.
- POODLE (Padding Oracle On Downgraded Legacy Encryption)와 같은 취약점이 발견되어 보안상 취약합니다. 


### TLS 1.2:

- 2008년에 발표되었습니다.
- TLS 1.1에서 발견된 취약점을 보완하고, 보안성을 향상시켰습니다.
- RC4와 3DES를 사용하지 않고, AES 암호화 방식을 지원합니다.
- GCM (Galois/Counter Mode) 모드에서 암호화를 수행합니다.
- Perfect Forward Secrecy (PFS)를 지원하며, 보안성이 크게 향상되었습니다.
- BEAST 및 POODLE과 같은 취약점을 해결하였습니다.

### TLS 1.3:

- 2018년에 발표되었습니다.
- TLS 1.2에서 발견된 취약점을 보완하고, 보안성을 향상시켰습니다.
- Handshake 과정에서의 지연을 줄이고, 웹 사이트 로딩 속도를 높였습니다.
- 암호화 방식과 프로토콜 구조가 개선되어, 보안성이 향상되었습니다.
- 0-RTT (Zero Round Trip Time) 기능을 도입하여, 연결 시간을 단축시켰습니다.