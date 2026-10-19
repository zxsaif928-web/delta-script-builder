<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Delta Script Builder</title>

  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      min-height: 100vh;
      padding: 24px;
      background: #090b12;
      color: #f3f4f6;
      font-family: Arial, sans-serif;
    }

    .container {
      max-width: 850px;
      margin: 40px auto;
      padding: 24px;
      background: #121622;
      border: 1px solid #283047;
      border-radius: 18px;
    }

    h1 {
      color: #8b9cff;
      text-align: center;
    }

    p {
      color: #b5bdcf;
      line-height: 1.8;
    }

    label {
      display: block;
      margin: 18px 0 8px;
    }

    input, select, textarea, button {
      width: 100%;
      padding: 13px;
      border-radius: 9px;
      border: 1px solid #30394e;
      background: #0b0e17;
      color: white;
      font-size: 15px;
    }

    textarea {
      min-height: 220px;
      resize: vertical;
      direction: ltr;
      text-align: left;
      font-family: monospace;
    }

    button {
      margin-top: 14px;
      border: none;
      background: #6578ff;
      font-weight: bold;
      cursor: pointer;
    }

    button:hover {
      background: #8290ff;
    }

    .secondary {
      background: #252d41;
    }

    .status {
      margin-top: 12px;
      color: #aab7ff;
    }
  </style>
</head>

<body>
  <main class="container">
    <h1>Delta Script Builder</h1>

    <p>
      محرر Lua لإنشاء نماذج تجريبية لمشاريع Roblox الخاصة بك.
      اكتب الكود، ثم أنشئ نسخة للنسخ أو الحفظ.
    </p>

    <label for="scriptName">اسم السكربت</label>
    <input
      id="scriptName"
      maxlength="60"
      value="MyScript"
      placeholder="اكتب اسم السكربت"
    >

    <label for="template">القالب</label>
    <select id="template">
      <option value="hello">Hello World</option>
      <option value="part">إنشاء Part تجريبي</option>
      <option value="custom">كود فارغ</option>
    </select>

    <label for="editor">محرر Lua</label>
    <textarea id="editor" spellcheck="false">print("Hello from my Lua project!")</textarea>

    <button id="generate">تجهيز السكربت</button>
    <button id="copy" class="secondary">نسخ الكود</button>
    <button id="download" class="secondary">تحميل ملف Lua</button>

    <p id="status" class="status" aria-live="polite"></p>
  </main>

  <script>
    const editor = document.getElementById("editor");
    const template = document.getElementById("template");
    const scriptName = document.getElementById("scriptName");
    const status = document.getElementById("status");

    const templates = {
      hello: 'print("Hello from my Lua project!")',

      part: [
        'local part = Instance.new("Part")',
        'part.Name = "DemoPart"',
        'part.Size = Vector3.new(4, 1, 4)',
        'part.Position = Vector3.new(0, 5, 0)',
        'part.Anchored = true',
        'part.Parent = workspace'
      ].join("\n"),

      custom: "-- اكتب كود Lua هنا"
    };

    template.addEventListener("change", () => {
      editor.value = templates[template.value];
      status.textContent = "";
    });

    document.getElementById("generate").addEventListener("click", () => {
      const name = scriptName.value.trim();

      if (!name) {
        status.textContent = "اكتب اسمًا للسكربت أولًا.";
        return;
      }

      if (!editor.value.trim()) {
        status.textContent = "المحرر فارغ.";
        return;
      }

      status.textContent = "تم تجهيز " + name + ".lua";
    });

    document.getElementById("copy").addEventListener("click", async () => {
      try {
        await navigator.clipboard.writeText(editor.value);
        status.textContent = "تم نسخ الكود.";
      } catch {
        editor.focus();
        editor.select();
        status.textContent = "حدّد الكود وانسخه يدويًا.";
      }
    });

    document.getElementById("download").addEventListener("click", () => {
      const name = scriptName.value.trim()
        .replace(/[^a-zA-Z0-9_-]/g, "_") || "MyScript";

      const blob = new Blob(
        [editor.value],
        { type: "text/plain;charset=utf-8" }
      );

      const url = URL.createObjectURL(blob);
      const link = document.createElement("a");

      link.href = url;
      link.download = name + ".lua";
      link.click();

      setTimeout(() => URL.revokeObjectURL(url), 1000);
      status.textContent = "بدأ تنزيل الملف.";
    });
  </script>
</body>
</html>
