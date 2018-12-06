<template>
  <div>
    <button @click='print_zpl()'>Click me to print some zpl</button>
    <button @click='get_printers()'>View Available Printers</button>
    <div v-for="printer in printers">
      <div @click='select_printer()' class='printer_line_item'>{{ printer }}<div
    </div>

    </div>
  </div>
</template>

<script>
import axios from "axios";
import qz from "qz-tray";
import sha256 from 'sha256'

// let print_data = [
//   {
//   type: 'image',
//   data: './assets/logo.png'
// }
// ]

let print_data = [
'^XA\n' ,
'^LH16,12\n' ,
'^LL1212\n' ,
'^FT 581, 403 ^AUN  ^FD0000^FS ^FX Configurable Text 1\n',
'^FT 500, 557 ^AUN  ^FDC047^FS ^FX Configurable Text 2\n',
'^FT  20, 313 ^A0N, 28, 20 ^FDTest Sender^FS ^FX Shipper Name\n',
'^FT  20, 343 ^A0N, 28, 20 ^FD6023 W Olympiad Ln^FS ^FX Shipper Address\n',
'^FT  20, 373 ^A0N, 28, 20 ^FDHerriman UT 84096-4653^FS ^FX Shipper Address\n',
'^FT  20, 403 ^A0N, 28, 20 ^FD^FS ^FX Shipper Address\n',
'^FT  20, 433 ^A0N, 28, 20 ^FD^FS ^FX Shipper City State Zip\n',
'^FT  20, 463 ^A0N, 28, 20 ^FD^FS ^FX Shipper Country Code\n',
'^FT 88, 594 ^A0N, 32, 35 ^FDMARKY MARK^FS ^FX Consignee Contact\n',
'^FT 88, 631 ^A0N, 32, 35 ^FD2759 CARNATION LN^FS ^FX Consignee Name\n',
'^FT 88, 669 ^A0N, 32, 35 ^FDHENDERSON NV 89074-2402^FS ^FX Consignee Address 1\n',
'^FT 88, 707 ^A0N, 32, 35 ^FD^FS ^FX Consignee Address 2\n',
'^FT 88, 745 ^A0N, 32, 35 ^FD^FS ^FX Consignee Address 3\n',
'^FT 88, 783 ^A0N, 32, 35 ^FD^FS ^FX Consignee City State Zip\n',
'^FT 172,  25 ^AQN ^FD^FS ^FX USPS Postage (& Fees) Paid\n',
'^FT 172,  25 ^A0N, 28, 21 ^FD^FS ^FX LXWXH\n',
'^FT 172,  51 ^AQN ^FDUS POSTAGE^FS ^FX USPS Postage (& Fees) Paid\n',
'^FT 172,  77 ^A0N, 28, 21 ^FD12/01/2018^FS ^FX Shipment Date\n',
'^FT 172, 105 ^A0N, 28, 21 ^FDFrom 84096^FS ^FX Origin zip\n',
'^FT 172, 133 ^A0N, 28, 21 ^FD0 lbs 10 ozs^FS ^FX Package Weight\n',
'^FT 172, 161 ^A0N, 28, 21 ^FD^FS ^FX Destination zone\n',
'^FT 172, 189 ^A0N, 28, 21 ^FD^FS ^FX Insured Text\n',
'^FT 364, 134 ^AQN, 22, 14 ^FB270,1,0,C ^FDPitney Bowes^FS ^FX PB\n',
'^FT 364, 160 ^AQN, 22, 11 ^FB270,1,0,C ^FDComPlsPrice^FS ^FX PB\n',
'^FT 364, 189 ^A0N, 32, 22 ^FB270,1,0,C ^FDNO SURCHARGE^FS ^FX PB\n',
'^FT 501, 134 ^A0N, 22, 20 ^FB270,1,0,R ^FD022W0001259233^FS ^FX PB\n',
'^FT 501, 160 ^A0N, 22, 20 ^FB270,1,0,R ^FD ^FS ^FX PB\n',
'^FT 501, 186 ^A0N, 22, 20 ^FB270,1,0,R ^FD9015270708^FS ^FX PB\n',
'^BY2,2.0,^FS\n',
'^FT 373, 93 ^B7N,4,4,7,,N^FH^FD^FS ^FX Indicia\n',
'^FO350,10^GFA,4500,4500,50,,::::::::003IFJC07BB83C001F98067E0600201C1DFE30CFF033FC3FC30C3CFCF003C0780460781E61801E2203C003IF30330C,:::003IFJC07FBB80041FF9879F87FFC1E23DFFC0C30F303CCIF0CFFC0C3FBC47C04007FE0679F9FFDDC1E03IF30330C,:::003IFJC07BB83FF81F87FF9867863FFC1DDFF3C3C03ICFFC00CFF030C03C3BC7FF867FF87E199DE3FE003IF30330C,::003IFJC07BB83FF81iF9DE3FE003IF30330C,003IFJC07FB847F81iGFE21DFE03IF30330C,::003IFJC07C47FBF81iFE23E01C03IF30330C,:::003IFJC07FBBFC039iGFDDFFC023IF30330C,:003IFJC07FBBFC039gLFC03gRFDDFFC023IF30330C,003IFJC07FBBFC039WF81IF03FEI03FF03FJ03gGFDDFFC023IF30330C,003IFJC07B87C07F9WFC1FFE03FCI03FF03FK0gFE21FC1FE3IF30330C,003IFJC07B87C07F9WFC1FFE07F8J0FF03FK07YFE21FC1FE3IF30330C,003IFJC07B87C07F9WFC0FFC0FEK03F03FK03YFE21FC1FE3IF30330C,003IFJC07B87C07F9WFC0FFC0FE00F803F03FK01YFE21FC1FE3IF30330C,003IFJC07FB840401WFE07FC0FC07FE03F03F03FF00gFE21DC003IF30330C,003IFJC07FB840401WFE07FC0FC07FF03F03F03FF80gFE21DC003IF30330C,003IFJC07FB840401WFE03F81F80IF81F03F03FFC07YFE21DC003IF30330C,003IFJC0443C07FC1XF03F83F81IFC1F03F03FFC07XF9C3E1FFE3IF30330C,:003IFJC0443C07FC1XF83F03F83IFC0F03F03FFE07XF9C3E1FFE3IF30330C,:003IFJC04407C3F81XFC1F07F83IFC0F03F03FFE07XF9C1FC3E03IF30330C,003IFJC04407C3F81XFC0E07F83IFC0F03F03FFE07XF9C1FC3E03IF30330C,003IFJC04407C3F81XFC0E0FF83IFC0F03F03FFE07XF9C1FC3E03IF30330C,:003IFJC0443FC4001XFE0C0FF81IFC0F03F03FFC07XFE21FC1C03IF30330C,003IFJC0443FC4001XFE0C0FF81IFC1F03F03FFC07XFE21FC1C03IF30330C,003IFJC0443FC4001XFE041FF80IF81F03F03FFC0YFE21FC1C03IF30330C,003IFJC0443FC4001XFE041FFC07FF03F03F03FF80YFE21FC1C03IF30330C,003IFJC04407BFF81YF003FFC07FE03F03F03FF01YF9C1FDFFE3IF30330C,003IFJC04407BFF81YF003FFC03FC03F03F03FC01YF9C1FDFFE3IF30330C,003IFJC04407BFF81YF803FFEK03F03FK03YF9C1FDFFE3IF30330C,003IFJC07C40047C1YFC07IF8J0FF03FK07gFDC01DFC3IF30330C,003IFJC07C40047C1YFC07IF8I01FF03FK0gGFDC01DFC3IF30330C,003IFJC07C40047C1YFC0JFEI03FF03FJ03gGFDC01DFC3IF30330C,003IFJC07C40047C1YFC0KF800IF03FJ0gHFDC01DFC3IF30330C,003IFJC07FC407BF9iGFE2020203IF30330C,:::003IFJC044007BFF9iF8221FE003IF30330C,:::003IFJC07C3BC7F81iGFC23DFE03IF30330C,::003IFJC07FB804401iF9C1FE0023IF30330C,:003IFJC07FB804401FE601E787F9C3C1E1DC03FF3C3FC3C0F30C03CFFC43FF843C7E7E61FE1F99C1FE0023IF30330C,:003IFJC07FC3BFC799F86007867E0201E1IF030F3303F033C0C0F3F3FFBBFF83F9E781F861F9FC23FE023IF30330C,:::003IFJC07C7B807818601E78061FDFE3C1E20FF3FF30CF003C0FCF03007BFC7C40787E1F86019FDE201E03IF30330C,:::,::::::::^FS\n',
'^BY3,2.0,^FS\n',
'^FT  60,1040 ^BCN,154,N,N,N,N^FD>;>842089074>89400109205328000041772^FS ^FX Tracking Barcode\n',
'^FT 0,1094 ^ATN ^FB776,1,0,C, ^FD9400 1092 0532 8000 0417 72^FS ^FX Tracking Barcode text\n',
'^FT 8,1148 ^A0N, 28, 21  ^FD^FS ^FX Bottom Custom Message text\n',
'^FT 8,1170 ^A0N, 28, 21  ^FD^FS ^FX Bottom Custom Message text\n',
'^FT 8,1192 ^A0N, 28, 21  ^FD^FS ^FX Bottom Custom Message text\n',
'^FT   0, 256 ^AUN  ^FB776,1,0,C, ^FH_^FDUSPS FIRST-CLASS PKG^FS ^FX USPS Mail Class\n',
'^FO20,20^GFA,2212,2212,14,!::::::::FF8gH01!::::::::::::::::FF8J03!::::::::FF8J03FE,:::::::::::::::::::::::::FF8J03WFC,::::::::FF8g07FC,::::::::::::::::FF8J03WFC,::::::::FF8J03FE,::::::::::::::::::::::::::::::::::::::::::::::::::::::OFE,::::::^FS\n',
'^FT   0, 856 ^ATN  ^FB776,1,0,C, ^FDUSPS TRACKING #^FS ^FX e/ USPS DELIVERY CONFIRMATION\n',
'^FT  40, 539 ^A0N, 24, 32  ^FD^FS ^FX Restricted Delivery\n',
'^FT 0, 296 ^APN  ^FD^FS ^FX Scheduled Delivery Date\n',
'^FO 748, 313 ^ADR ^FD^FS ^FX Custom Message\n',
'^FO 60,430 ^A0N 32,30 ^FD ^FS ^FX Endorsement\n',
'^FO 60,465 ^A0N 32,30 ^FD ^FS ^FX Endorsement\n',
'^FO 60,500 ^A0N 32,30 ^FD^FS ^FX Endorsement\n',
'^FT 567, 326 ^A0N 32,30 ^FD^FS ^FX Fragile Text\n',
'^LRY^FT5,805 ^GB 770,,65^FS\n',
'^FT60,800^AUN ^FDTEST LABEL - DO NOT MAIL^FS\n',
'^FT   0, 818 ^GB 776,0,12,B ^FS\n',
'^FT   0,1121 ^GB 776,0,12,B ^FS\n',
'^FT   0, 274 ^GB 776,0,6,B ^FS\n',
'^FT   0, 206 ^GB 776,0,6,B ^FS\n',
'^FT 153, 203 ^GB 0,203,4,B ^FS\n',
'^FT 496, 565 ^GB 111,51,1,B ^FS\n',
'^FO   0,   0 ^GB 776,1198,2,B ^FS\n',
'^LH0\n',
'^LRN\n',
'^XZ\n'
]



qz.api.setPromiseType(function promise(resolver) {
  return new Promise(resolver);
});
qz.api.setSha256Type(function(data) { return sha256(data); })
if (!qz.websocket.isActive()) {
  qz.websocket.connect().then(function() {
    alert("Connected!");
    console.log("connInfo", qz.websocket.getConnectionInfo());
  });
}
else{
  alert('websocket connection is active')
}

export default {
  name: "HelloWorld",
  data() {
    return {
      person: {},
      printers: {}
    };
  },
  methods: {
    print_zpl(){
      console.log('this', this)
      qz.printers.getDefault()
        .then(printer=>{
          console.log('this now', this)
          console.log('selected printer',printer)
          let zebra_printer = qz.configs.create(printer)
          qz.print(zebra_printer, print_data)
            .then((data)=>{
              // alert(print_data)
            })
        })
        .catch(error=>{
          alert('no printer could be found')
        })
    },
    get_printers(){
      qz.printers.find()  
        .then((data)=>{
          console.log('THIS', this)
          this.printers = data
          console.log('printers', data)
        })
        .catch(function(error){
          console.log(error)
        })
    }
  }
  // created() {
  //   axios({
  //     method: "POST",
  //     url: "http://localhost:3000/api/ehub/getLabel",
  //     data: {
  //       shipment: {
  //         to_location: {
  //           first_name: "Marky",
  //           last_name: "Mark",
  //           address1: "2759 Carnation Lane",
  //           city: "Las Vegas",
  //           state: "NV",
  //           country: "US",
  //           postal_code: "89074",
  //           email: "test@test.com"
  //         },
  //         from_location: {
  //           first_name: "Test",
  //           last_name: "Sender",
  //           address1: "6023 Olympiad Lane",
  //           city: "Herriman",
  //           state: "UT",
  //           country: "US",
  //           postal_code: "84096",
  //           email: "test@test.com"
  //         },
  //         parcels: [
  //           {
  //             length: 8,
  //             width: 8,
  //             height: 8,
  //             weight: 10
  //           }
  //         ],
  //         service_id: 157,
  //         label_format: "zpl",
  //         order_id: 564984
  //       }
  //     },
  //     headers:{
  //       'Content-Type':'application/json',
  //       'ehub-token':'Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJpYXQiOjE1MjY0NDIxMzEsImRhdGEiOnsidXNlciI6eyJpZCI6MzMsImN1c3RvbWVyX2lkIjoyMiwiZW1haWwiOiJ3b29kZGFuMjJAZ21haWwuY29tIn0sInNjb3BlcyI6WyJhcGlfcHVibGljIl19fQ.60grwINXW7DRqud2XTuQdJc_x4hjeMpZWOdGUg4P0BbaI4q590tOgljW816C5mAQKtui757F4NOKfTnHAJZIBA',
  //       'Authorization': 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoxMywiZW1haWwiOiJ3b29kZGFuMjJAZ21haWwuY29tIiwic3Vic2NyaXB0aW9uX2lkIjoxLCJ1c2VybmFtZSI6ImR3b29kIiwibmFtZSI6bnVsbCwiYWN0aXZlIjp0cnVlLCJ0b2tlbl9leHBpcmF0aW9uIjoiMjAxOC0xMi0wMVQyMToxODoxMS41NTBaIiwicGFzc3dvcmQiOiIkMmIkMTAka0lmWGtzTFQvd1dpa1VSbi9KdnVodW1taFQzMDJkZ0V4c1dnVWdrNC5YTmZ5L1Rnd01sZXkiLCJpYXQiOjE1NDM2OTcyOTF9.8ZyajomdQ0WIvaMFr2tiRTP5U3sQ9PRvhFBTd3Ave5I'
  //     }
  //   })
  //     .then(data => {
  //       console.log('shipment', data.data.body.shipment.parcels[0].postage_label.image_url);
  //       axios.get(data.data.body.shipment.parcels[0].postage_label.image_url)
  //         .then(response=>{
  //           this.person = response
  //         })
  //         .catch(error=>{
  //           alert(error)
  //         })
  //     })
  //     .catch(error => {
  //       alert(error);
  //     });
  // }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.printer_line_item {
  border: 2px black;
}
</style>
