<template >
  <div class="hello" >
  <v-container fluid>
    <table>
      <tr>
        <th colspan="4">Binary</th>
        <th colspan="2">Decimal</th>
        <th colspan="2">Ascii</th>
      </tr>
      <tr>
        <td colspan=4>
          <input type="text" class="binary-input" maxlength="8" v-model="binaryInput"/>
        </td>
        <td colspan=2>
          <button @click="increment(false)">-</button>
          <input type="text" class="decimal-input" maxlength="3" @keydown.left="increment(false)"  @keydown.right="increment(true)" v-model="base10"/>
          <button @click="increment(true)">+</button>
        </td>
        <td colspan=2>
          <input type="text" class="decimal-input" maxlength="1" v-model="ascii"/>
        </td>
      </tr>
      <tr class="spacer"></tr>
      <tr>
        <th colspan="8">Hex</th>
      </tr>
      <tr>
        <td colspan="4">
          <input type="text" class="decimal-input" maxlength="1" v-model="hex.split('')[0]"/>
        </td>
        <td colspan="4">
          <input type="text" class="decimal-input" maxlength=1 v-model="hex.split('')[1]"/>
        </td>
      </tr>
      <!--<tr>
        <td v-for="(bit, index) in bits" :key="index">
          <v-checkbox color="red" @change="bitsToString" :checked="bit.value" v-model="bit.value"></v-checkbox>
          <input type="checkbox" color="red" @change="bitsToString" :checked="bit.value" v-model="bit.value">
        </td>
      </tr>-->
      


      <tr>
        <td v-for="(bit, index) in bits" :key="index">
          <div class="bit-td" :class="{ asdf: bit.value }" v-on:click="bit.value = !bit.value; bitsToString()">
            {{toBase2(bit)}}
          </div>
        </td>
      </tr>
      <tr class="spacer"></tr>
      <tr>
        <td v-for="(bit, index) in bits" :class="{ wasd: bit.value }" v-on:click="bit.value = !bit.value; bitsToString()" :key="index" class="exponent">
          <!--{{toExponent(bit, index)}}-->
          {{getExponentValue(index)}}
        </td>
      </tr>

      <tr>
        <td class="position" v-for="(bit, index) in bits" :key="index">
          2^{{index}}
        </td>
      </tr>
    </table>

    <table>
      <tr>
        <th>
        Hex Value
        </th>
        <th>
        Bits
        </th>
      </tr>
      <tr v-for="(hexCode, index) in hexValues" :key="index">
        <td class="bit-td">
          {{hexCode}}
        </td>
        <td class="bit-td">
          {{hex2bin(hexCode)}}
        </td>
      </tr>
    </table>

    <table>
      <tr>
        <th>Decimal</th>
        <th>Character</th>
        <th>Description</th>
        <th>Binary</th>
      </tr>
      <tr v-for="(ascii, index) in asciiValues" :key="index">
        <td class="bit-td">
          {{ascii.dec}}
        </td>
        <td class="bit-td">
          {{ascii.char}}
        </td>
        <td class="bit-td">
          {{ascii.description}}
        </td>
        <td class="bit-td">
          {{dec2bin(index)}}
        </td>
      </tr>
    </table>

  </v-container>
  </div>
</template>

<script>
import _ from "lodash";
export default {
  name: "HelloWorld",
  watch: {
    binaryInput(newValue){ //replace with computed binary
      newValue.split('').map((bit, index) => {
        return this.bits[index].value = this.toBool(bit)
      })
    }
  },
  computed: {
    binary: {
      get(){},
      set(){}
    },
    base10: {
      get() {
        return this.bits.reduce((acc, bit, index) => {
          return acc += this.toExponent(bit, index)
        }, 0)
      },
      set(newValue) {
        //console.log("I do nothing " + newValue)
        //_.debounce(function() {this.binaryInput = new Number(newValue).toString(2)}, 1000)
        this.binaryInput = new Number(newValue).toString(2).split('').reverse().join('')
      }
    },
    ascii: {
      get(){
        return String.fromCharCode(this.base10)
      },
      set(newValue){
        this.base10 = newValue.charCodeAt(0)
      }
    },
    hex: {
      get() {
        return parseInt(this.binaryInput, 2).toString(16).toUpperCase();
      },
      set() {
        console.log("I do nothing")
      },
    }
  },
  methods: {
    increment(add){
      //https://jsfiddle.net/x53494ef/285/
      console.log(this.base10)
      if (add){
        this.base10 += 1
      } else {
        this.base10 -= 1
      }
    },
    hex2bin(hex){
      return ("0000" + (parseInt(hex, 16)).toString(2)).substr(-4);
    },
    dec2bin(dec){
      return ("00000000" + (parseInt(dec, 10)).toString(2)).substr(-8);
    },
    toBase2(bit){
      return 0+bit.value
    },
    toBool(bit){
      return !!+bit
    },
    toExponent(bit, position){
      if (this.toBase2(bit) === 0){
        return 0
      }
      return Math.pow((this.toBase2(bit) * 2), position)
    },
    getExponentValue(position){
      return Math.pow(2, position)
    },
    bitsToString() {
      const reduced = this.bits.map((bit) => { return this.toBase2(bit) })
      this.binaryInput = reduced.join('')
    }
  },
  data() {
    return {
      binaryInput: "",
      asciiTable: "",
      bits: [
        {value: false},
        {value: false},
        {value: true},
        {value: false},
        {value: true},
        {value: true},
        {value: true},
        {value: true},
      ],
      hexValues: ["0","1","2","3","4","5","6","7","8","9","A","B","C","D","E","F"],
      asciiValues: [
        {
          "dec": 0,
          "char": "NULL",
          "description": "(Null character)"
        },
        {
          "dec": 1,
          "char": "SOH",
          "description": "(Start of Header)"
        },
        {
          "dec": 2,
          "char": "STX",
          "description": "(Start of Text)"
        },
        {
          "dec": 3,
          "char": "ETX",
          "description": "(End of Text)"
        },
        {
          "dec": 4,
          "char": "EOT",
          "description": "(End of Transmission)"
        },
        {
          "dec": 5,
          "char": "ENQ",
          "description": "(Enquiry)"
        },
        {
          "dec": 6,
          "char": "ACK",
          "description": "(Acknowledgement)"
        },
        {
          "dec": 7,
          "char": "BEL",
          "description": "(Bell)"
        },
        {
          "dec": 8,
          "char": "BS",
          "description": "(Backspace)"
        },
        {
          "dec": 9,
          "char": "HT",
          "description": "(Horizontal Tab)"
        },
        {
          "dec": 10,
          "char": "LF",
          "description": "(Line feed)"
        },
        {
          "dec": 11,
          "char": "VT",
          "description": "(Vertical Tab)"
        },
        {
          "dec": 12,
          "char": "FF",
          "description": "(Form feed)"
        },
        {
          "dec": 13,
          "char": "CR",
          "description": "(Carriage return)"
        },
        {
          "dec": 14,
          "char": "SO",
          "description": "(Shift Out)"
        },
        {
          "dec": 15,
          "char": "SI",
          "description": "(Shift In)"
        },
        {
          "dec": 16,
          "char": "DLE",
          "description": "(Data link escape)"
        },
        {
          "dec": 17,
          "char": "DC1",
          "description": "(Device control 1)"
        },
        {
          "dec": 18,
          "char": "DC2",
          "description": "(Device control 2)"
        },
        {
          "dec": 19,
          "char": "DC3",
          "description": "(Device control 3)"
        },
        {
          "dec": 20,
          "char": "DC4",
          "description": "(Device control 4)"
        },
        {
          "dec": 21,
          "char": "NAK",
          "description": "(Negative acknowledgement)"
        },
        {
          "dec": 22,
          "char": "SYN",
          "description": "(Synchronous idle)"
        },
        {
          "dec": 23,
          "char": "ETB",
          "description": "(End of transmission block)"
        },
        {
          "dec": 24,
          "char": "CAN",
          "description": "(Cancel)"
        },
        {
          "dec": 25,
          "char": "EM",
          "description": "(End of medium)"
        },
        {
          "dec": 26,
          "char": "SUB",
          "description": "(Substitute)"
        },
        {
          "dec": 27,
          "char": "ESC",
          "description": "(Escape)"
        },
        {
          "dec": 28,
          "char": "FS",
          "description": "(File separator)"
        },
        {
          "dec": 29,
          "char": "GS",
          "description": "(Group separator)"
        },
        {
          "dec": 30,
          "char": "RS",
          "description": "(Record separator)"
        },
        {
          "dec": 31,
          "char": "US",
          "description": "(Unit separator)"
        },
        {
          "dec": 32,
          "char": "",
          "description": "(Space)"
        },
        {
          "dec": 33,
          "char": "!",
          "description": "(Exclamation mark)"
        },
        {
          "dec": 34,
          "char": "",
          "description": "(Quotation mark ; quotes)"
        },
        {
          "dec": 35,
          "char": "#",
          "description": "(Number sign)"
        },
        {
          "dec": 36,
          "char": "$",
          "description": "(Dollar sign)"
        },
        {
          "dec": 37,
          "char": "%",
          "description": "(Percent sign)"
        },
        {
          "dec": 38,
          "char": "&",
          "description": "(Ampersand)"
        },
        {
          "dec": 39,
          "char": "",
          "description": "(Apostrophe)"
        },
        {
          "dec": 40,
          "char": "(",
          "description": "(round brackets or parentheses)"
        },
        {
          "dec": 41,
          "char": ")",
          "description": "(round brackets or parentheses)"
        },
        {
          "dec": 42,
          "char": "*",
          "description": "(Asterisk)"
        },
        {
          "dec": 43,
          "char": "+",
          "description": "(Plus sign)"
        },
        {
          "dec": 44,
          "char": ",",
          "description": "(Comma)"
        },
        {
          "dec": 45,
          "char": "-",
          "description": "(Hyphen)"
        },
        {
          "dec": 46,
          "char": ".",
          "description": "(Dot , full stop)"
        },
        {
          "dec": 47,
          "char": "/",
          "description": "(Slash)"
        },
        {
          "dec": 48,
          "char": 0,
          "description": "(number zero)"
        },
        {
          "dec": 49,
          "char": 1,
          "description": "(number one)"
        },
        {
          "dec": 50,
          "char": 2,
          "description": "(number two)"
        },
        {
          "dec": 51,
          "char": 3,
          "description": "(number three)"
        },
        {
          "dec": 52,
          "char": 4,
          "description": "(number four)"
        },
        {
          "dec": 53,
          "char": 5,
          "description": "(number five)"
        },
        {
          "dec": 54,
          "char": 6,
          "description": "(number six)"
        },
        {
          "dec": 55,
          "char": 7,
          "description": "(number seven)"
        },
        {
          "dec": 56,
          "char": 8,
          "description": "(number eight)"
        },
        {
          "dec": 57,
          "char": 9,
          "description": "(number nine)"
        },
        {
          "dec": 58,
          "char": ":",
          "description": "(Colon)"
        },
        {
          "dec": 59,
          "char": ";",
          "description": "(Semicolon)"
        },
        {
          "dec": 60,
          "char": "<",
          "description": "(Less-than sign)"
        },
        {
          "dec": 61,
          "char": "=",
          "description": "(Equals sign)"
        },
        {
          "dec": 62,
          "char": ">",
          "description": "(Greater-than sign ; Inequality)"
        },
        {
          "dec": 63,
          "char": "?",
          "description": "(Question mark)"
        },
        {
          "dec": 64,
          "char": "@",
          "description": "(At sign)"
        },
        {
          "dec": 65,
          "char": "A",
          "description": "(Capital A)"
        },
        {
          "dec": 66,
          "char": "B",
          "description": "(Capital B)"
        },
        {
          "dec": 67,
          "char": "C",
          "description": "(Capital C)"
        },
        {
          "dec": 68,
          "char": "D",
          "description": "(Capital D)"
        },
        {
          "dec": 69,
          "char": "E",
          "description": "(Capital E)"
        },
        {
          "dec": 70,
          "char": "F",
          "description": "(Capital F)"
        },
        {
          "dec": 71,
          "char": "G",
          "description": "(Capital G)"
        },
        {
          "dec": 72,
          "char": "H",
          "description": "(Capital H)"
        },
        {
          "dec": 73,
          "char": "I",
          "description": "(Capital I)"
        },
        {
          "dec": 74,
          "char": "J",
          "description": "(Capital J)"
        },
        {
          "dec": 75,
          "char": "K",
          "description": "(Capital K)"
        },
        {
          "dec": 76,
          "char": "L",
          "description": "(Capital L)"
        },
        {
          "dec": 77,
          "char": "M",
          "description": "(Capital M)"
        },
        {
          "dec": 78,
          "char": "N",
          "description": "(Capital N)"
        },
        {
          "dec": 79,
          "char": "O",
          "description": "(Capital O)"
        },
        {
          "dec": 80,
          "char": "P",
          "description": "(Capital P)"
        },
        {
          "dec": 81,
          "char": "Q",
          "description": "(Capital Q)"
        },
        {
          "dec": 82,
          "char": "R",
          "description": "(Capital R)"
        },
        {
          "dec": 83,
          "char": "S",
          "description": "(Capital S)"
        },
        {
          "dec": 84,
          "char": "T",
          "description": "(Capital T)"
        },
        {
          "dec": 85,
          "char": "U",
          "description": "(Capital U)"
        },
        {
          "dec": 86,
          "char": "V",
          "description": "(Capital V)"
        },
        {
          "dec": 87,
          "char": "W",
          "description": "(Capital W)"
        },
        {
          "dec": 88,
          "char": "X",
          "description": "(Capital X)"
        },
        {
          "dec": 89,
          "char": "Y",
          "description": "(Capital Y)"
        },
        {
          "dec": 90,
          "char": "Z",
          "description": "(Capital Z)"
        },
        {
          "dec": 91,
          "char": "[",
          "description": "(square brackets or box brackets)"
        },
        {
          "dec": 92,
          "char": "\\",
          "description": "(Backslash)"
        },
        {
          "dec": 93,
          "char": "]",
          "description": "(square brackets or box brackets)"
        },
        {
          "dec": 94,
          "char": "^",
          "description": "(Caret or circumflex accent)"
        },
        {
          "dec": 95,
          "char": "_",
          "description": "(underscore , understrike , underbar or low line)"
        },
        {
          "dec": 96,
          "char": "`",
          "description": "(Grave accent)"
        },
        {
          "dec": 97,
          "char": "a",
          "description": "(Lowercase a )"
        },
        {
          "dec": 98,
          "char": "b",
          "description": "(Lowercase b )"
        },
        {
          "dec": 99,
          "char": "c",
          "description": "(Lowercase c )"
        },
        {
          "dec": 100,
          "char": "d",
          "description": "(Lowercase d )"
        },
        {
          "dec": 101,
          "char": "e",
          "description": "(Lowercase e )"
        },
        {
          "dec": 102,
          "char": "f",
          "description": "(Lowercase f )"
        },
        {
          "dec": 103,
          "char": "g",
          "description": "(Lowercase g )"
        },
        {
          "dec": 104,
          "char": "h",
          "description": "(Lowercase h )"
        },
        {
          "dec": 105,
          "char": "i",
          "description": "(Lowercase i )"
        },
        {
          "dec": 106,
          "char": "j",
          "description": "(Lowercase j )"
        },
        {
          "dec": 107,
          "char": "k",
          "description": "(Lowercase k )"
        },
        {
          "dec": 108,
          "char": "l",
          "description": "(Lowercase l )"
        },
        {
          "dec": 109,
          "char": "m",
          "description": "(Lowercase m )"
        },
        {
          "dec": 110,
          "char": "n",
          "description": "(Lowercase n )"
        },
        {
          "dec": 111,
          "char": "o",
          "description": "(Lowercase o )"
        },
        {
          "dec": 112,
          "char": "p",
          "description": "(Lowercase p )"
        },
        {
          "dec": 113,
          "char": "q",
          "description": "(Lowercase q )"
        },
        {
          "dec": 114,
          "char": "r",
          "description": "(Lowercase r )"
        },
        {
          "dec": 115,
          "char": "s",
          "description": "(Lowercase s )"
        },
        {
          "dec": 116,
          "char": "t",
          "description": "(Lowercase t )"
        },
        {
          "dec": 117,
          "char": "u",
          "description": "(Lowercase u )"
        },
        {
          "dec": 118,
          "char": "v",
          "description": "(Lowercase v )"
        },
        {
          "dec": 119,
          "char": "w",
          "description": "(Lowercase w )"
        },
        {
          "dec": 120,
          "char": "x",
          "description": "(Lowercase x )"
        },
        {
          "dec": 121,
          "char": "y",
          "description": "(Lowercase y )"
        },
        {
          "dec": 122,
          "char": "z",
          "description": "(Lowercase z )"
        },
        {
          "dec": 123,
          "char": "{",
          "description": "(curly brackets or braces)"
        },
        {
          "dec": 124,
          "char": "|",
          "description": "(vertical-bar, vbar, vertical line or vertical slash)"
        },
        {
          "dec": 125,
          "char": "}",
          "description": "(curly brackets or braces)"
        },
        {
          "dec": 126,
          "char": "~",
          "description": "(Tilde ; swung dash)"
        },
        {
          "dec": 127,
          "char": "DEL",
          "description": "(Delete)"
        },
        {
          "dec": 128,
          "char": "Ç",
          "description": "(Majuscule C-cedilla)"
        },
        {
          "dec": 129,
          "char": "ü",
          "description": "(letter \"u\" with umlaut or diaeresis ; \"u-umlaut\")"
        },
        {
          "dec": 130,
          "char": "é",
          "description": "(letter \"e\" with acute accent or \"e-acute\")"
        },
        {
          "dec": 131,
          "char": "â",
          "description": "(letter \"a\" with circumflex accent or \"a-circumflex\")"
        },
        {
          "dec": 132,
          "char": "ä",
          "description": "(letter \"a\" with umlaut or diaeresis ; \"a-umlaut\")"
        },
        {
          "dec": 133,
          "char": "à",
          "description": "(letter \"a\" with grave accent)"
        },
        {
          "dec": 134,
          "char": "å",
          "description": "(letter \"a\" with a ring)"
        },
        {
          "dec": 135,
          "char": "ç",
          "description": "(Minuscule c-cedilla)"
        },
        {
          "dec": 136,
          "char": "ê",
          "description": "(letter \"e\" with circumflex accent or \"e-circumflex\")"
        },
        {
          "dec": 137,
          "char": "ë",
          "description": "(letter \"e\" with umlaut or diaeresis ; \"e-umlaut\")"
        },
        {
          "dec": 138,
          "char": "è",
          "description": "(letter \"e\" with grave accent)"
        },
        {
          "dec": 139,
          "char": "ï",
          "description": "(letter \"i\" with umlaut or diaeresis ; \"i-umlaut\")"
        },
        {
          "dec": 140,
          "char": "î",
          "description": "(letter \"i\" with circumflex accent or \"i-circumflex\")"
        },
        {
          "dec": 141,
          "char": "ì",
          "description": "(letter \"i\" with grave accent)"
        },
        {
          "dec": 142,
          "char": "Ä",
          "description": "(letter \"A\" with umlaut or diaeresis ; \"A-umlaut\")"
        },
        {
          "dec": 143,
          "char": "Å",
          "description": "(Capital letter \"A\" with a ring)"
        },
        {
          "dec": 144,
          "char": "É",
          "description": "(Capital letter \"E\" with acute accent or \"E-acute\")"
        },
        {
          "dec": 145,
          "char": "æ",
          "description": "(Latin diphthong \"ae\" in lowercase)"
        },
        {
          "dec": 146,
          "char": "Æ",
          "description": "(Latin diphthong \"AE\" in uppercase)"
        },
        {
          "dec": 147,
          "char": "ô",
          "description": "(letter \"o\" with circumflex accent or \"o-circumflex\")"
        },
        {
          "dec": 148,
          "char": "ö",
          "description": "(letter \"o\" with umlaut or diaeresis ; \"o-umlaut\")"
        },
        {
          "dec": 149,
          "char": "ò",
          "description": "(letter \"o\" with grave accent)"
        },
        {
          "dec": 150,
          "char": "û",
          "description": "(letter \"u\" with circumflex accent or \"u-circumflex\")"
        },
        {
          "dec": 151,
          "char": "ù",
          "description": "(letter \"u\" with grave accent)"
        },
        {
          "dec": 152,
          "char": "ÿ",
          "description": "(Lowercase letter \"y\" with diaeresis)"
        },
        {
          "dec": 153,
          "char": "Ö",
          "description": "(letter \"O\" with umlaut or diaeresis ; \"O-umlaut\")"
        },
        {
          "dec": 154,
          "char": "Ü",
          "description": "(letter \"U\" with umlaut or diaeresis ; \"U-umlaut\")"
        },
        {
          "dec": 155,
          "char": "ø",
          "description": "(slashed zero or empty set)"
        },
        {
          "dec": 156,
          "char": "£",
          "description": "(Pound sign ; symbol for the pound sterling)"
        },
        {
          "dec": 157,
          "char": "Ø",
          "description": "(slashed zero or empty set)"
        },
        {
          "dec": 158,
          "char": "×",
          "description": "(multiplication sign)"
        },
        {
          "dec": 159,
          "char": "ƒ",
          "description": "(function sign ; f with hook sign ; florin sign )"
        },
        {
          "dec": 160,
          "char": "á",
          "description": "(letter \"a\" with acute accent or \"a-acute\")"
        },
        {
          "dec": 161,
          "char": "í",
          "description": "(letter \"i\" with acute accent or \"i-acute\")"
        },
        {
          "dec": 162,
          "char": "ó",
          "description": "(letter \"o\" with acute accent or \"o-acute\")"
        },
        {
          "dec": 163,
          "char": "ú",
          "description": "(letter \"u\" with acute accent or \"u-acute\")"
        },
        {
          "dec": 164,
          "char": "ñ",
          "description": "(letter \"n\" with tilde ; enye)"
        },
        {
          "dec": 165,
          "char": "Ñ",
          "description": "(letter \"N\" with tilde ; enye)"
        },
        {
          "dec": 166,
          "char": "ª",
          "description": "(feminine ordinal indicator)"
        },
        {
          "dec": 167,
          "char": "º",
          "description": "(masculine ordinal indicator)"
        },
        {
          "dec": 168,
          "char": "¿",
          "description": "(Inverted question marks)"
        },
        {
          "dec": 169,
          "char": "®",
          "description": "(Registered trademark symbol)"
        },
        {
          "dec": 170,
          "char": "¬",
          "description": "(Logical negation symbol)"
        },
        {
          "dec": 171,
          "char": "½",
          "description": "(One half)"
        },
        {
          "dec": 172,
          "char": "¼",
          "description": "(Quarter or one fourth)"
        },
        {
          "dec": 173,
          "char": "¡",
          "description": "(Inverted exclamation marks)"
        },
        {
          "dec": 174,
          "char": "«",
          "description": "(Angle quotes or guillemets)"
        },
        {
          "dec": 175,
          "char": "»",
          "description": "(Guillemets or angle quotes)"
        },
        {
          "dec": 176,
          "char": "░",
          "description": ""
        },
        {
          "dec": 177,
          "char": "▒",
          "description": ""
        },
        {
          "dec": 178,
          "char": "▓",
          "description": ""
        },
        {
          "dec": 179,
          "char": "│",
          "description": "(Box drawing character)"
        },
        {
          "dec": 180,
          "char": "┤",
          "description": "(Box drawing character)"
        },
        {
          "dec": 181,
          "char": "Á",
          "description": "(Capital letter \"A\" with acute accent or \"A-acute\")"
        },
        {
          "dec": 182,
          "char": "Â",
          "description": "(letter \"A\" with circumflex accent or \"A-circumflex\")"
        },
        {
          "dec": 183,
          "char": "À",
          "description": "(letter \"A\" with grave accent)"
        },
        {
          "dec": 184,
          "char": "©",
          "description": "(Copyright symbol)"
        },
        {
          "dec": 185,
          "char": "╣",
          "description": "(Box drawing character)"
        },
        {
          "dec": 186,
          "char": "║",
          "description": "(Box drawing character)"
        },
        {
          "dec": 187,
          "char": "╗",
          "description": "(Box drawing character)"
        },
        {
          "dec": 188,
          "char": "╝",
          "description": "(Box drawing character)"
        },
        {
          "dec": 189,
          "char": "¢",
          "description": "(Cent symbol)"
        },
        {
          "dec": 190,
          "char": "¥",
          "description": "(YEN and YUAN sign)"
        },
        {
          "dec": 191,
          "char": "┐",
          "description": "(Box drawing character)"
        },
        {
          "dec": 192,
          "char": "└",
          "description": "(Box drawing character)"
        },
        {
          "dec": 193,
          "char": "┴",
          "description": "(Box drawing character)"
        },
        {
          "dec": 194,
          "char": "┬",
          "description": "(Box drawing character)"
        },
        {
          "dec": 195,
          "char": "├",
          "description": "(Box drawing character)"
        },
        {
          "dec": 196,
          "char": "─",
          "description": "(Box drawing character)"
        },
        {
          "dec": 197,
          "char": "┼",
          "description": "(Box drawing character)"
        },
        {
          "dec": 198,
          "char": "ã",
          "description": "(Lowercase letter \"a\" with tilde or \"a-tilde\")"
        },
        {
          "dec": 199,
          "char": "Ã",
          "description": "(Capital letter \"A\" with tilde or \"A-tilde\")"
        },
        {
          "dec": 200,
          "char": "╚",
          "description": "(Box drawing character)"
        },
        {
          "dec": 201,
          "char": "╔",
          "description": "(Box drawing character)"
        },
        {
          "dec": 202,
          "char": "╩",
          "description": "(Box drawing character)"
        },
        {
          "dec": 203,
          "char": "╦",
          "description": "(Box drawing character)"
        },
        {
          "dec": 204,
          "char": "╠",
          "description": "(Box drawing character)"
        },
        {
          "dec": 205,
          "char": "═",
          "description": "(Box drawing character)"
        },
        {
          "dec": 206,
          "char": "╬",
          "description": "(Box drawing character)"
        },
        {
          "dec": 207,
          "char": "¤",
          "description": "(generic currency sign)"
        },
        {
          "dec": 208,
          "char": "ð",
          "description": "(Lowercase letter \"eth\")"
        },
        {
          "dec": 209,
          "char": "Ð",
          "description": "(Capital letter \"Eth\")"
        },
        {
          "dec": 210,
          "char": "Ê",
          "description": "(letter \"E\" with circumflex accent or \"E-circumflex\")"
        },
        {
          "dec": 211,
          "char": "Ë",
          "description": "(letter \"E\" with umlaut or diaeresis ; \"E-umlaut\")"
        },
        {
          "dec": 212,
          "char": "È",
          "description": "(letter \"E\" with grave accent)"
        },
        {
          "dec": 213,
          "char": "ı",
          "description": "(lowercase dot less i)"
        },
        {
          "dec": 214,
          "char": "Í",
          "description": "(Capital letter \"I\" with acute accent or \"I-acute\")"
        },
        {
          "dec": 215,
          "char": "Î",
          "description": "(letter \"I\" with circumflex accent or \"I-circumflex\")"
        },
        {
          "dec": 216,
          "char": "Ï",
          "description": "(letter \"I\" with umlaut or diaeresis ; \"I-umlaut\")"
        },
        {
          "dec": 217,
          "char": "┘",
          "description": "(Box drawing character)"
        },
        {
          "dec": 218,
          "char": "┌",
          "description": "(Box drawing character)"
        },
        {
          "dec": 219,
          "char": "█",
          "description": "(Block)"
        },
        {
          "dec": 220,
          "char": "▄",
          "description": "(Bottom half block)"
        },
        {
          "dec": 221,
          "char": "¦",
          "description": "(vertical broken bar)"
        },
        {
          "dec": 222,
          "char": "Ì",
          "description": "(letter \"I\" with grave accent)"
        },
        {
          "dec": 223,
          "char": "▀",
          "description": "(Top half block)"
        },
        {
          "dec": 224,
          "char": "Ó",
          "description": "(Capital letter \"O\" with acute accent or \"O-acute\")"
        },
        {
          "dec": 225,
          "char": "ß",
          "description": "(letter \"Eszett\" ; \"scharfes S\" or \"sharp S\")"
        },
        {
          "dec": 226,
          "char": "Ô",
          "description": "(letter \"O\" with circumflex accent or \"O-circumflex\")"
        },
        {
          "dec": 227,
          "char": "Ò",
          "description": "(letter \"O\" with grave accent)"
        },
        {
          "dec": 228,
          "char": "õ",
          "description": "(letter \"o\" with tilde or \"o-tilde\")"
        },
        {
          "dec": 229,
          "char": "Õ",
          "description": "(letter \"O\" with tilde or \"O-tilde\")"
        },
        {
          "dec": 230,
          "char": "µ",
          "description": "(Lowercase letter \"Mu\" ; micro sign or micron)"
        },
        {
          "dec": 231,
          "char": "þ",
          "description": "(Lowercase letter \"Thorn\")"
        },
        {
          "dec": 232,
          "char": "Þ",
          "description": "(Capital letter \"thorn\")"
        },
        {
          "dec": 233,
          "char": "Ú",
          "description": "(Capital letter \"U\" with acute accent or \"U-acute\")"
        },
        {
          "dec": 234,
          "char": "Û",
          "description": "(letter \"U\" with circumflex accent or \"U-circumflex\")"
        },
        {
          "dec": 235,
          "char": "Ù",
          "description": "(letter \"U\" with grave accent)"
        },
        {
          "dec": 236,
          "char": "ý",
          "description": "(Lowercase letter \"y\" with acute accent)"
        },
        {
          "dec": 237,
          "char": "Ý",
          "description": "(Capital letter \"Y\" with acute accent)"
        },
        {
          "dec": 238,
          "char": "¯",
          "description": "(macron symbol)"
        },
        {
          "dec": 239,
          "char": "´",
          "description": "(Acute accent)"
        },
        {
          "dec": 240,
          "char": "­",
          "description": "(Hyphen)"
        },
        {
          "dec": 241,
          "char": "±",
          "description": "(Plus-minus sign)"
        },
        {
          "dec": 242,
          "char": "‗",
          "description": "(underline or underscore)"
        },
        {
          "dec": 243,
          "char": "¾",
          "description": "(three quarters)"
        },
        {
          "dec": 244,
          "char": "¶",
          "description": "(paragraph sign or pilcrow)"
        },
        {
          "dec": 245,
          "char": "§",
          "description": "(Section sign)"
        },
        {
          "dec": 246,
          "char": "÷",
          "description": "(The division sign ; Obelus)"
        },
        {
          "dec": 247,
          "char": "¸",
          "description": "(cedilla)"
        },
        {
          "dec": 248,
          "char": "°",
          "description": "(degree symbol )"
        },
        {
          "dec": 249,
          "char": "¨",
          "description": "(Diaeresis)"
        },
        {
          "dec": 250,
          "char": "·",
          "description": "(Interpunct or space dot)"
        },
        {
          "dec": 251,
          "char": "¹",
          "description": "(superscript one)"
        },
        {
          "dec": 252,
          "char": "³",
          "description": "(cube or superscript three)"
        },
        {
          "dec": 253,
          "char": "²",
          "description": "(Square or superscript two)"
        },
        {
          "dec": 254,
          "char": "■",
          "description": "(black square)"
        },
        {
          "dec": 255,
          "char": "nbsp",
          "description": "(non-breaking space or no-break space)"
        }
      ]
    };
  },
  mounted(){
    this.bitsToString()
  }
};
</script>

<style lang="scss" scoped>
  td {
    min-width: 30px;
    border: 1px solid grey;
    user-select: none;
  }

  table {
    border: 1px solid grey;
    padding: 10px;
    background-color: #262626;
    color: white;
  }

  input{
    font-size: 2rem;
    text-align: center;
    outline: none;
  }

  .decimal-input {
    width: 50px;
  }

  .binary-input{
    max-width: 150px;
  }

  .bit-td{
    color: white;
    background-color: #6E6E6E;
    font-size: 2rem;
    padding-left: 15px;
    padding-right: 15px;
    cursor: pointer;
    transition: background-color linear .3s;
  }

  .asdf{
    color: white;
    background-color: #4EC5F7;
  }


  .wasd{
    color: white;
    background-color: #8C67D7;
    cursor: pointer;
  }

  .spacer {
    height: 20px;
  }

  .spacer-small{
    height: 10px;
  }

  .position {
    background-color: #404040;
  }
</style>
