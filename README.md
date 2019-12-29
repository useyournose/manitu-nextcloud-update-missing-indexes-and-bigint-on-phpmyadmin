# manitu - nextcloud update, missing indexes and bigint on phpmyadmin

[error.jpg](error.jpg)

if you know the message before a nextcloud update, telling you, that indexes are missing and columns are int not bigint, here's how to solve it, with no access to a shell or cronjobs.
You could also put https://apps.nextcloud.com/apps/occweb on your instance, but it wouldn't work when the instance is in maintenance mode.

Keep in mind that "nc_4366_" seems to be a prefix, and would be different for your installation.

## 1. Indexes
regular command:
sudo -u www-data php occ db:add-missing-indices

mysql alternative:
ALTER TABLE `nc_4366_share` ADD UNIQUE `owner_index` (`id`, `uid_owner`) USING BTREE;
ALTER TABLE `nc_4366_share` ADD UNIQUE `initiator_index` (`id`, `uid_initiator`) USING BTREE;

## 2. column data type
set the server to maintenance mode:
https://docs.nextcloud.com/server/9.0/admin_manual/maintenance/enable_maintenance.html

regular command:
sudo -u www-data php occ maintenance:mode --on

alternative:
find the file /config/config.php
download it, 
add "maintenance = true," if no maintenance line is available.
and upload it again to enable maintenance mode.

validate it by browsing on your nextcloud instance.

regular command:
sudo -u www-data php occ db:convert-filecache-bigint

mysql alternative:
alter table nc_4366_filecache MODIFY COLUMN mtime BIGINT(8);
alter table nc_4366_filecache MODIFY COLUMN storage_mtime BIGINT(8);

regular command:
sudo -u www-data php occ maintenance:mode --off

alternative:
find the /config/config.php
change "maintenance = false," and upload again.
https://www.manitu.de/service/faq/entry/1288/Wie-kann-ich-den-Wartungsmodus-meiner-Nextcloud-ownCloud-beenden/
validate it by browsing on your nextcloud instance.

