### System Backup

<pre><code>
#!/bin/bash

BACKUP_DIR="/backup"
mkdir -p "$BACKUP_DIR"
DATE=$(date +%Y%m%d-%H%M%S)

# 디스크 이미지 생성 (예: /dev/sda)
dd if=/dev/sda of="$BACKUP_DIR/disk_backup_$DATE.img" bs=4M status=progress

# MBR/GRUB 백업
dd if=/dev/sda of="$BACKUP_DIR/mbr_backup_$DATE.img" bs=512 count=1

echo "디스크 백업 완료: $BACKUP_DIR"
</code></pre>


### Popup
<pre><code>
function showPopup() {
    if (document.getElementById("custom-popup")) return; // 중복 생성 방지

    let popup = document.createElement("div");
    popup.id = "custom-popup";
    popup.style.position = "fixed";
    popup.style.top = "0";
    popup.style.left = "0";
    popup.style.width = "100%";
    popup.style.height = "100%";
    popup.style.background = "rgba(0, 0, 0, 0.5)";
    popup.style.display = "flex";
    popup.style.justifyContent = "center";
    popup.style.alignItems = "center";
    popup.style.zIndex = "999999";

    let popupContent = document.createElement("div");
    popupContent.style.background = "white";
    popupContent.style.padding = "20px";
    popupContent.style.borderRadius = "10px";
    popupContent.style.boxShadow = "0 0 10px rgba(0, 0, 0, 0.3)";
    popupContent.style.textAlign = "center";
    popupContent.style.width = "300px";

    let message = document.createElement("p");
    message.innerText = "지금은 09:30입니다!";
    popupContent.appendChild(message);

    let closeButton = document.createElement("button");
    closeButton.innerText = "닫기";
    closeButton.style.marginTop = "10px";
    closeButton.style.padding = "5px 10px";
    closeButton.style.cursor = "pointer";
    closeButton.style.border = "none";
    closeButton.style.background = "red";
    closeButton.style.color = "white";
    closeButton.style.borderRadius = "5px";

    closeButton.addEventListener("click", function () {
        document.body.removeChild(popup);
    });

    popupContent.appendChild(closeButton);
    popup.appendChild(popupContent);
    document.body.appendChild(popup);
}

// 매일 09:30에 실행되도록 설정
function schedulePopup() {
    let now = new Date();
    let targetTime = new Date();
    targetTime.setHours(9, 30, 0, 0); // 09:30:00

    // 현재 시간이 09:30 이후라면, 다음 날 09:30에 실행하도록 설정
    if (now > targetTime) {
        targetTime.setDate(targetTime.getDate() + 1);
    }

    let timeUntilPopup = targetTime - now; // 밀리초 단위 계산

    setTimeout(() => {
        showPopup(); // 09:30에 팝업 띄우기

        // 이후에는 매일 24시간(86400000ms)마다 실행
        setInterval(showPopup, 86400000);
    }, timeUntilPopup);
}

// 확장 프로그램이 실행될 때마다 `schedulePopup` 호출
schedulePopup();
</code></pre>