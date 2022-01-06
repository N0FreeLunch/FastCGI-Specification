3. Protocol Basics
3.1 Notation
We use C language notation to define protocol message formats. All structure elements are defined in terms of the unsigned char type, and are arranged so that an ISO C compiler lays them out in the obvious manner, with no padding. The first byte defined in the structure is transmitted first, the second byte second, etc.

프로토콜 메시지 형식을 정의하기 위해 C 언어 표기법을 사용합니다. 모든 구조 요소는 unsigned char 유형으로 정의되며 ISO C 컴파일러가 패딩 없이 명확한 방식으로 배치하도록 배열됩니다. 구조에 정의된 첫 번째 바이트가 먼저 전송되고 두 번째 바이트가 두 번째로 전송되는 식입니다.

We use two conventions to abbreviate our definitions.

정의를 축약하기 위해 두 가지 규칙을 사용합니다.

First, when two adjacent structure components are named identically except for the suffixes "B1" and "B0," it means that the two components may be viewed as a single number, computed as B1<<8 + B0. The name of this single number is the name of the components, minus the suffixes. This convention generalizes in an obvious way to handle numbers represented in more than two bytes.

첫째, 접미사 "B1"과 "B0"을 제외하고 두 개의 인접한 구조 구성요소가 동일하게 명명될 때, 이는 두 구성요소를 B1<<8 + B0으로 계산되는 단일 숫자로 볼 수 있음을 의미합니다. 이 단일 숫자의 이름은 접미사를 뺀 구성 요소의 이름입니다. 이 규칙은 2바이트 이상으로 표현되는 숫자를 처리하기 위해 분명한 방식으로 일반화됩니다.

Second, we extend C structs to allow the form

둘째, C 구조체를 확장하여 다음 형식을 허용합니다.

        struct {
            unsigned char mumbleLengthB1;
            unsigned char mumbleLengthB0;
            ... /* other stuff */
            unsigned char mumbleData[mumbleLength];
        };
        
meaning a structure of varying length, where the length of a component is determined by the values of the indicated earlier component or components.

구성 요소의 길이는 표시된 이전 구성 요소 또는 구성 요소의 값에 의해 결정되는 다양한 길이의 구조를 의미합니다.
