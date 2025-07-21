# -<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <title>شركة النور للأجهزة الكهربائية</title>
    <style>
        body { font-family: Arial; direction: rtl; text-align: center; }
        .device { border: 1px solid #ccc; margin: 10px; padding: 10px; display: inline-block; width: 250px; vertical-align: top; }
        img { width: 100%; height: 150px; object-fit: cover; }
        input, textarea { width: 90%; padding: 8px; margin: 5px 0; }
        button {
            padding: 8px 12px;
            background-color: #28a745;
            color: white;
            border: none;
            cursor: pointer;
        }
        button:hover {
            background-color: #218838;
        }
        .device button {
            margin-top: 10px;
            display: block;
            width: 90%;
            margin-left: auto;
            margin-right: auto;
        }
        .delete-btn {
            background-color: #dc3545;
        }
        .delete-btn:hover {
            background-color: #c82333;
        }
        .edit-btn {
            background-color: #007bff;
        }
        .edit-btn:hover {
            background-color: #0056b3;
        }
    </style>
</head>
<body>
    <h1>شركة النور للأجهزة الكهربائية</h1>

    <div>
        <h3>أضف جهاز جديد</h3>
        <input type="text" id="name" placeholder="اسم الجهاز"><br>
        <input type="text" id="price" placeholder="السعر"><br>
        <input type="text" id="image" placeholder="رابط الصورة"><br>
        <textarea id="desc" placeholder="الوصف"></textarea><br>
        <button id="mainButton" onclick="addDevice()">إضافة</button>
    </div>

    <hr>

    <div id="deviceList"></div>

    <script>
        const deviceList = document.getElementById("deviceList");
        const mainButton = document.getElementById("mainButton");

        let isEditing = false;
        let editingIndex = null;

        window.onload = function () {
            const savedDevices = JSON.parse(localStorage.getItem("devices") || "[]");
            savedDevices.forEach((device, index) => renderDevice(device, index));
        }

        function addDevice() {
            const name = document.getElementById("name").value.trim();
            const price = document.getElementById("price").value.trim();
            const image = document.getElementById("image").value.trim();
            const desc = document.getElementById("desc").value.trim();

            if (!name || !price || !image) {
                alert("يرجى ملء جميع الحقول");
                return;
            }

            if (!image.startsWith("http")) {
                alert("يرجى إدخال رابط صورة صحيح يبدأ بـ http أو https");
                return;
            }

            const devices = JSON.parse(localStorage.getItem("devices") || "[]");

            if (isEditing) {
                devices[editingIndex] = { name, price, image, desc };
                localStorage.setItem("devices", JSON.stringify(devices));
                refreshDevices();
                isEditing = false;
                editingIndex = null;
                mainButton.textContent = "إضافة";
                alert("تم حفظ التعديل");
            } else {
                const newDevice = { name, price, image, desc };
                devices.push(newDevice);
                localStorage.setItem("devices", JSON.stringify(devices));
                renderDevice(newDevice, devices.length - 1);
                alert("تمت إضافة الجهاز بنجاح");
            }

            // تفريغ الحقول
            document.getElementById("name").value = "";
            document.getElementById("price").value = "";
            document.getElementById("image").value = "";
            document.getElementById("desc").value = "";
        }

        function renderDevice(deviceData, index) {
            const device = document.createElement("div");
            device.className = "device";
            device.innerHTML = `
                <img src="${deviceData.image}" alt="${deviceData.name}">
                <h3>${deviceData.name}</h3>
                <p><strong>السعر:</strong> ${deviceData.price} ريال</p>
                <p>${deviceData.desc}</p>
                <button class="edit-btn" onclick="editDevice(${index})">تعديل</button>
                <button class="delete-btn" onclick="deleteDevice(${index})">حذف</button>
            `;
            deviceList.appendChild(device);
        }

        function refreshDevices() {
            deviceList.innerHTML = "";
            const devices = JSON.parse(localStorage.getItem("devices") || "[]");
            devices.forEach((device, index) => renderDevice(device, index));
        }

        function deleteDevice(index) {
            const devices = JSON.parse(localStorage.getItem("devices") || "[]");
            devices.splice(index, 1);
            localStorage.setItem("devices", JSON.stringify(devices));
            refreshDevices();
        }

        function editDevice(index) {
            const devices = JSON.parse(localStorage.getItem("devices") || "[]");
            const device = devices[index];

            document.getElementById("name").value = device.name;
            document.getElementById("price").value = device.price;
            document.getElementById("image").value = device.image;
            document.getElementById("desc").value = device.desc;

            isEditing = true;
            editingIndex = index;
            mainButton.textContent = "حفظ التعديل";
        }
    </script>
</body>
</html>
