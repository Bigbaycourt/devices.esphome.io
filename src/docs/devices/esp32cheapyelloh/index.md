globals:
  # the code vectors of three buttons A_ON, A_OFF and B_ON
- id: a_on
  type: std::vector<uint32_t>
  initial_value: '{ 0xbfcf6c, 0xbe821c, 0xb1d75c, 0xb593ac }'
- id: a_off
  type: std::vector<uint32_t>
  initial_value: '{ 0xbd2e2c, 0xb4ed8c, 0xb2b6bc, 0xb3017c }'
- id: b_on
  type: std::vector<uint32_t>
  initial_value: '{ 0xbb3535, 0xb87af5, 0xbca0e5, 0xba5c05 }'

script:
    # - select vector with bs_button
    # - lookup code from vector with idx
    # - call remote_transmitter
    # - update idx
  - id: bs_transmitter
    mode: single
    parameters:
      bs_button: int
    then:
      - remote_transmitter.transmit_brennenstuhl:
          code: !lambda |-
            uint32_t code = 0;
            static int32_t idx = 0; // rolling index
            switch(bs_button) {
              case 10: { // A_ON
                code=id(a_on)[idx];
                break;
              }
              case 11: { // A_OFF
                code=id(a_off)[idx];
                break;
              }
              case 20: {  // B_ON
                code=id(b_on)[idx];
                break;
              }
            }
            idx = (idx + 1) & 3;
            return code;
  ...
