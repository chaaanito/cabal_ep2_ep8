# Cabal Online (Self-Hosted Server) in ALPINE LINUX


## Table of Contents

   - [Docker](#Docker)
   - [Zerotier-Installation](#Zerotier-Installation)
   - [Cabal-Server-Installation](#Cabal-Server-Installation)
   - [Episode2](#Episode2)
   - [Episode8](#Episode8)

## Docker

   Install [Docker]
   Make sure your OS is updated && upgraded. 
   Uncomment your Community Repository to access Docker.
   ```cmd
      apk add --update docker openrc
   ```
   ```cmd
      rc-update add docker boot
   ```
   ```cmd
      service docker start
   ```
   ```cmd
      service docker status
   ```
   ```cmd
      apk add docker-compose
   ```

## Zerotier-Installation
Installing ZeroTier Self-Hosted Controller w/ UI
1. Clone the repository:
   ```cmd
   git clone https://github.com/chaaanito/Cabal_Origin.git
   
2. Installing ZTNET Container:
   ```cmd
   cd Cabal_Origin/ztnet
   docker-compose up -d
   
3. Access ZTNET Controller
   ```
   http://localhost:3000/
*Create an Account and Create a Network

4. Installing Local Zerotier Client to join the network you have created.
   ```cmd
   wget https://dl-cdn.alpinelinux.org/alpine/v3.17/community/x86_64/zerotier-one-openrc-1.10.2-r0.apk
   wget https://dl-cdn.alpinelinux.org/alpine/v3.17/community/x86_64/zerotier-one-1.10.2-r0.apk
   apk add zerotier-one-1.10.2-r0.apk 
   apk add zerotier-one-openrc-1.10.2-r0.apk 
   rc-update add zerotier-one boot

5. Change Local ZeroTier Client Port
   ```cmd
   nano /var/lib/zerotier-one/local.conf
   ```
   COPY & PASTE in PuTTy (We need to Change 9993->9994 since ZT Controller uses it already)
   ```cmd
   {
     "settings": {
       "primaryPort": 9994
     }
   }
   ```
6. Start Zerotier
   ```cmd
   service zerotier-one start
   ```
7. Joining ZeroTier Network
   ```
   zerotier-cli join (yourNetworkId)
   ```

## Cabal-Server-Installation
## Episode2:
   ```cmd
   cd cabal_server_ep2
   ```
### Environment

   Copy .env.example to .env and configurate .env
   ```
   cp .env.example .env
   nano .env
   ```
   ```
   DB_PASSWORD=StronG_db_passw0rd // Use a strong password for mssql with special symbols, numbers, and uppercase symbols
   DB_PORT=1433 // Database port
   PUBLIC_IP=this.your.server.ip // IP server <------ use your Zerotier Assigned IP.
   EXP_RATE=100 // Enter EXP rate multiplier, e.g. 5 for 5x 
   SEXP_RATE=100 // Enter Skill EXP rate multiplier
   CEXP_RATE=100 // Enter Craft EXP rate multiplier
   DROP_RATE=2 // Enter drop rate multiplier (over 5 is bad)
   ALZ_RATE=100 // Enter Alz rate multiplier
   BALZ_RATE=100 // Enter Alz bomb rate multiplier
   PEXP_RATE=100 // Enter Pet EXP multiplier
   ITEMS_PER_DROP=2 // Enter number of items per drop
   CONFIG_TYPE=1
   ```

#### CONFIG_TYPE
   ```
   1 - Mercury (1 chan. Premium)
   2 - Venus  (1 chan. PK)
   3 - Mars (1 chan. War)
   4 - Jupiter (1 chan. Tierra Gloriosa)
   5 - Saturn (2 chan. Normal/TG)
   6 - Neptune (3 chan. Normal/War/Tierra Gloriosa)
   7 - Pluto (3 chan. PK/War/Tierra Gloriosa)
   8 - Leo (4 chan. Normal/PK/Prem War/Tierra Gloriosa)
   9 - Sirius (4 chan. Normal/PK/War/Tierra Gloriosa)
   10 - Draco (5 chan. Normal/Prem/Prem PK/Prem War/TG)
   11 - Test Server (1 chan. PK)
   12 - Duality (2 server, 1 norm and 1 War channel) DONT WORK STABLE
   20 - Divinity (3 server, 1 norm and 1 PK channel each) DONT WORK STABLE
   ```

   Otherwise, your container will not start.

## Build containers
   ```cmd
   docker-compose build
   ```
## Database

   Up container
   ```cmd
   docker-compose up -d mssql
   ```

   Restore database
   ```cmd
   docker-compose exec mssql sh restoredb.sh
   ```
   default registered user 
   username "admin" password "admin"
## Server

Start server
   ```cmd
   docker-compose up -d server
   ```
   ```cmd
   docker-compose exec server sh /home/cabal/cabal_config.sh
   ```
### Stop server
   ```cmd
   docker-compose down server
   ```
### Service logs

   ```cmd
   server/logs
   ```

### GM panel/Register Account http://localhost
   ```cmd
   docker-compose up -d --build site nginx
   docker-compose exec site composer install --no-cache
   docker-compose exec site php artisan migrate --force
   docker-compose exec site chmod -R 777 storage
   ```

### GM panel Set GM
   ```cmd
   docker-compose exec site php artisan set:gm (login|ID)
   ```
#### example
   ```cmd
   docker-compose exec site php artisan set:gm admin
   ```
##########################################################################
## Episode8: 
   ```cmd
   cd cabal_server_ep8
   ```

### Environment

   Copy .env.example to .env and configurate .env

   Use a strong password for mssql with special symbols, numbers, and uppercase symbols

   Otherwise, your container will not start.

### Database

   ```cmd
   docker-compose up -d --build mssql
   docker-compose exec mssql sh restoredb.sh //enter password db from .env
   ```

### Server

   ```cmd
   docker-compose up -d --build server
   ```

If you don't use all chanels, comment 

   ```docker

   RUN cabal_create_server 1
   RUN cabal_create_chanel 1 1
   #RUN cabal_create_chanel 1 2
   #RUN cabal_create_chanel 1 3
   #RUN cabal_create_chanel 1 4
   #RUN cabal_create_chanel 1 5
   #RUN cabal_create_chanel 1 6
   #RUN cabal_create_chanel 1 7

   ```

   in server/Dockerfile

### Service status

   ```cmd
   docker-compose exec server ps -x
   ```

### Start N.WAR for Cron Job
   ##start
   ```cmd
   docker exec -it cabal_server-server-1 /usr/bin/WorldSvr_01_07
   ```
   ##stop
   ```cmd
   docker exec cabal_server-server-1 pkill WorldSvr_01_07
   ```
##########################################################################
###Database Adjustments
# SP   dbo.cabal_tool_registerAccount
 ```cmd
USE [Account]
GO
/****** Object:  StoredProcedure [dbo].[cabal_tool_registerAccount]    Script Date: 10/12/2023 8:34:44 am ******/
SET ANSI_NULLS OFF
GO
SET QUOTED_IDENTIFIER ON
GO
ALTER  PROCEDURE [dbo].[cabal_tool_registerAccount]   (@id varchar(32),  @password  varchar(256))
AS
begin tran
	declare @UserNum as int

	insert into cabal_auth_table( ID, Password, Login, AuthType, IdentityNo ) 
	values(@id, pwdencrypt(@password), '0', 1, '7700000000000' )

	set @UserNum = @@identity


	insert into cabal_charge_auth(usernum, type, expiredate, payminutes,ServiceKind)
	values(@UserNum, 1, DATEADD(day, 1000, getdate()), 0,1)
	
	insert into CabalCash..cashaccount (id,UserNum,Cash,CashBonus,UpdateDateTime) values(@id,@UserNum,0,0,GETDATE())

	select @UserNum as usernum
commit
 ```
## Account & Server01 - Table - cabal_newCharData_table
 ```cmd
update cabal_newCharData_table set WarpBField=1023, MapsBField=1023
update cabal_new_character_data set WarpBField=1023, MapsBField=1023
 ```

## Server01 - cabal_sp_newchar
```cmd
DECLARE @WarExp INT = 30;
DECLARE @Alz INT = 1000000; -- Example value for Alz
DECLARE @WarpBField INT = 1023; -- Example value for WarpBField
DECLARE @MapsBField INT = 1023; -- Example value for MapsBField
DECLARE @Reputation INT = 5000; -- Example value for MapsBField
INSERT INTO cabal_character_table
(
	CharacterIdx, Name,
	LEV, [EXP], [STR], DEX, [INT], PNT, Rank, Alz,
	WorldIdx, [Position], Style, 
	HP, MP, SP, SwdPNT, MagPNT, RankEXP, Flags, WarpBField, MapsBField, Reputation
)
SELECT
	@CharacterIdx, @Name,
	LEV, [EXP], [STR], [DEX], [INT], [PNT], Rank, @Alz,
	WorldIdx, Position, @Style +88,
	HP, MP, SP, SwdPNT, MagPNT, RankEXP, Flags, @WarpBField, @MapsBField , @Reputation
FROM cabal_new_character_data
WHERE ClassType = @StyleMastery
INSERT INTO cabal_WarExp_Table (CharacterIdx,WarExp)
SELECT @CharacterIdx, @WarExp
 ```
## where @styles are
```cmd
+8 = Class Rank 2
+16 = Class Rank 3
+24 = Class Rank 4
+32 = Class Rank 5
+40 = Class Rank 6
+48 = Class Rank 7
+56 = Class Rank 8
+64 = Class Rank 9
+72 = Class Rank 10
+80 = Class Rank 11
+88 = Class Rank 12
+96 = Class Rank 13
+104 = Class Rank 14
+112 = Class Rank 15
+120 = Class Rank 16
+128 = Class Rank 17
+136 = Class Rank 18
+144 = Class Rank 19
+152 = Class Rank 20
 ```
