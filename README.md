# Bash-Scripting-Automation-Linux-Server
This is a writeup of a practical project to automate Linux server administration tasks, which includes the following:
1. [System Updates and Patch Installation]()
2. [Automated Backups]()
3. [Disk Space Monitoring]()
4. [Cron Jobs Integration]()


## System Updates and Patch Installation
- Create a Bash script (e.g., `system_update.sh`) that performs updates and installs patches automatically
  ```
  #!/bin/bash
  # system_update.sh
  # This script updates the package lists and upgrades installed packages.
  
  # Update package lists
  sudo apt update -y
  
  # Upgrade all packages (for Debian/Ubuntu systems; use yum/dnf for CentOS/RHEL)
  sudo apt upgrade -y
  
  # Optionally, clean up outdated packages
  sudo apt autoremove -y
  ```


## Automated Backups
- Create a backup script (e.g., `backup.sh`) that handles backing up Apache logs and MySQL databases
  ```
  #!/bin/bash
  # backup.sh
  # This script creates backups for Apache logs and MySQL database dumps.
  
  # Variables
  DATE=$(date +%Y-%m-%d)
  BACKUP_DIR="/backup"
  APACHE_LOG_DIR="/var/log/apache2"
  MYSQL_USER="root"
  MYSQL_PASSWORD="your_password"
  DATABASE_NAME="your_database"
  
  # Create backup directories if they don't exist
  mkdir -p "$BACKUP_DIR/apache2"
  mkdir -p "$BACKUP_DIR/mysql"
  
  # Backup Apache logs
  cp "$APACHE_LOG_DIR/access.log" "$BACKUP_DIR/apache2/access_$DATE.log"
  cp "$APACHE_LOG_DIR/error.log" "$BACKUP_DIR/apache2/error_$DATE.log"
  
  # Backup MySQL database
  mysqldump -u "$MYSQL_USER" -p"$MYSQL_PASSWORD" "$DATABASE_NAME" > "$BACKUP_DIR/mysql/${DATABASE_NAME}_$DATE.sql"
  ```


## Disk Space Monitoring
- Develop a monitoring script (e.g., `monitor.sh`) to check system resource usage and send email alerts if thresholds are exceeded
  ```
  #!/bin/bash
  # monitor.sh
  # This script monitors disk space and sends an email alert if usage exceeds a defined threshold.
  
  # Set the threshold percentage
  THRESHOLD=90
  
  # Get current disk usage percentage for the root (/) partition
  USAGE=$(df / | tail -1 | awk '{print $5}' | sed 's/%//')
  
  # Check if the usage exceeds the threshold
  if [ "$USAGE" -gt "$THRESHOLD" ]; then
      # Compose alert message
      SUBJECT="Disk Space Alert on $(hostname)"
      MESSAGE="Warning: Disk usage on $(hostname) is at ${USAGE}%."
      
      # Send email alert (ensure mailutils is installed and configured)
      echo "$MESSAGE" | mail -s "$SUBJECT" your_email@example.com
  fi
  ```

## Cron Jobs Integration
- Before scheduling the scripts to run automatically using cron jobs, run each script manually to verify proper functionality
- To schedule the scripts cron job automation, open the crontab editor:
  ```
  crontab -e
  ```
- Add entries for each script:
  ```
  # Run system updates daily at 2 AM
  0 2 * * * /path/to/system_update.sh >> /var/log/system_update.log 2>&1
  
  # Run backups weekly on Sunday at 3 AM
  0 3 * * 0 /path/to/backup.sh >> /var/log/backup.log 2>&1
  
  # Run the monitoring script every 15 minutes
  */15 * * * * /path/to/monitor.sh >> /var/log/monitor.log 2>&1
  ```
- After scheduling, check the log files (e.g., /var/log/system_update.log) to verify that cron jobs are executing correctly


