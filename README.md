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