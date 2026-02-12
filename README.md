<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8" />
  <title>ระบบตรวจสอบผลสอบ</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    body { font-family: system-ui, sans-serif; background:#f5f7fa; }
    .card {
      max-width:420px;
      margin:80px auto;
      background:#fff;
      padding:24px;
      border-radius:16px;
      box-shadow:0 10px 25px rgba(0,0,0,.1);
    }
    h1 { text-align:center; margin-bottom:16px; }
    input {
      width:100%;
      padding:12px;
      font-size:16px;
      border-radius:10px;
      border:1px solid #ccc;
    }
    button {
      width:100%;
      margin-top:12px;
      padding:12px;
      font-size:16px;
      border-radius:10px;
      border:none;
      background:#2563eb;
      color:#fff;
      cursor:pointer;
    }
    .result {
      margin-top:20px;
      text-align:center;
      font-size:18px;
    }
    .pass { color:green; font-weight:600; }
    .fail { color:red; font-weight:600; }
    .absent { color:#f59e0b; font-weight:600; }
  </style>
</head>
<body>
  <div class="card">
    <h1>ตรวจสอบผลสอบ</h1>
    <input id="studentId" placeholder="กรอกเลขประจำตัวนักเรียน" />
    <button onclick="checkResult()">ตรวจสอบ</button>
    <div id="result" class="result"></div>
  </div>
<script>
    // 🔧 ข้อมูลตัวอย่าง (ครู/แอดมินแก้ตรงนี้)
    const students = {
  "51305": { name: "นางสาวชาลิสา แท่นมณี", status: "ผ่าน", score: 6 },
  "51429": { name: "นายวสวัต ดั่นธนสาร", status: "ผ่าน", score: 8 },
  "51581": { name: "นางสาวปภาวรินทร์ ชุมภูราช", status: "ผ่าน", score: 6 },
  "51769": { name: "นายจิรวัฒน์ เส้งลั่น", status: "ผ่าน", score: 10 },
  "51770": { name: "นายณฐกร แก้วจันทร์", status: "ผ่าน", score: 7 },
  "51775": { name: "นายพงศ์พิพัฒน์ ภิรมย์", status: "ผ่าน", score: 7 },
  "51777": { name: "นายวรพล หนูกล่ำ", status: "ผ่าน", score: 7 },
  "51778": { name: "นายอภิสร เพอแสละ", status: "ผ่าน", score: 10 },
  "51780": { name: "นางสาวชญานี ทองรักษ์", status: "ผ่าน", score: 8 },
  "51782": { name: "นางสาวณปภัสร์ ชูชื่น", status: "ผ่าน", score: 8 },
  "51783": { name: "นางสาวณภัทรสร โชโต", status: "ผ่าน", score: 10 },
  "51785": { name: "นางสาวทรรศนีย์ ครูนิลอาจ", status: "ผ่าน", score: 9 },
  "51788": { name: "นางสาวนภัสสร ประยูร", status: "ผ่าน", score: 9 },
  "51790": { name: "นางสาวนันทวัน ศรีไหม", status: "ผ่าน", score: 8 },
  "51791": { name: "นางสาวปุณยาพร พูลทัศน์", status: "ไม่ผ่าน", score: 5 },
  "51792": { name: "นางสาวภคนันท์ ชูแก้ว", status: "ผ่าน", score: 10 },
  "51793": { name: "นางสาวภิญญา คงสีปาน", status: "ผ่าน", score: 9 },
  "51796": { name: "นางสาวอนัญญา จู่เซ่งเจริญ", status: "ผ่าน", score: 10 },
  "51818": { name: "นางสาวมนัสนันท์ จิตประพันธ์", status: "ผ่าน", score: 9 },
  "51824": { name: "นางสาวอาธารี ยี่โชติช่วง", status: "ผ่าน", score: 8 },
  "54214": { name: "นายทรงพล จิระนิล", status: "ไม่ผ่าน", score: 5 },
  "54216": { name: "นางสาวกวินนธิดา เจริญพักตร์", status: "ผ่าน", score: 10 },
  "54218": { name: "นางสาวณิชกานต์ เพ็ชรสงคราม", status: "ผ่าน", score: 8 },
  "54219": { name: "นางสาวปาริชาต อินทร์เอม", status: "ผ่าน", score: 8 },
  "54220": { name: "นางสาวเปี่ยมขวัญ เข้ทอง", status: "ไม่ผ่าน", score: 5 },
  "54221": { name: "นางสาวยุวดี เกื้อเส้ง", status: "ผ่าน", score: 6 },
  "54222": { name: "นางสาวอุษามณี จันทร์แก้ว", status: "ไม่ผ่าน", score: 5 },
};
function checkResult() {
      const id = document.getElementById("studentId").value.trim();
      const box = document.getElementById("result");

      if (!id) {
        box.innerHTML = "กรุณากรอกเลขประจำตัวนักเรียน";
        box.className = "result fail";
        return;
      }

      if (!students[id]) {
        box.innerHTML = "ไม่พบข้อมูลนักเรียน";
        box.className = "result fail";
        return;
      }

      const s = students[id];

      if (s.status === "ผ่าน") {
        box.innerHTML = `
          ยินดีด้วย 🎉<br>
          ${s.name}<br>
          <span class="pass">สอบผ่าน</span><br>
          คะแนน: ${s.score}
        `;
        box.className = "result pass";

      } else if (s.status === "ขาดสอบ") {
        box.innerHTML = `
          ${s.name}<br>
          <span class="absent">ขาดสอบ</span>
        `;
        box.className = "result";

      } else {
        box.innerHTML = `
          พยายามอีกนิดนะ 💪<br>
          ${s.name}<br>
          <span class="fail">สอบไม่ผ่าน</span><br>
          คะแนน: ${s.score}
        `;
        box.className = "result fail";
      }
    }
  </script>
</body>
</html>
