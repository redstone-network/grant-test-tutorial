# pallet-permission-capture
## 1 alice create capture config by create_capture_config, then submit transaction 
![](./img/1.jpg)
the params is 
* BOB
* CHARLIE
* DAVE
* 2

## 2 alice find private key lost, let bob to call get_account_permissions, create a proposal for give the account permission to friends.
![](./img/2.jpg)

## 3 charlie agree proposal  (2 is the min permission threshold, now bob and charlie agree, match the pass conditions), then alice's tranfer must be agree by alice's friends

get activeCaptures executeProposalId by permissionCapture.activeCaptures(ALICE)
![](./img/3.jpg)
vote the activeCaptures proposal, now executeProposalId is 0
![](./img/4.jpg)

## 4 dave agree proposal
![](./img/5.jpg)

## 5 check the map mapPermissionTaken, find there is alice address in map.
![](./img/6.jpg)

## 6 alice safeTransfer to bob 5000000000000 token
![](./img/7.jpg)

## 7 bob agree the call hash
get alice call hash by permissionCapture.activeCalls, now is 0x88b20b87232415b479b49446ce02c3f2d4e6baeadc361b5b1e04b86c467b8843
![](./img/8.jpg)

vote the call hash
![](./img/9.jpg)

## 8 charlie agree the call hash
![](./img/10.jpg)

## 9 get the transfer event
![](./img/11.jpg)

## ref
* `create_get_account_permissions` - A friend initiates a proposal, and the ownership of the application address is transferred to the status of friend management.
* `cancel_get_account_permissions` - A friend cancels a proposal initiated by himself.
* `vote` - When the account is in takeover state, friends can use this method to vote on the address transaction application.
* `create_capture_config` - The user creates a power grab configuration and writes his friend list into this method.
* `operational_voting` - vote for call hash of permission owner's.

## more info: 
see testcase in https://github.com/redstone-network/redstone-node/blob/main/pallets/permission-capture/src/tests.rs

see api doc in https://redstone-node-doc.vercel.app/pallet_permission_capture/pallet/struct.Pallet.html#

# pallet-defense 

## 1 test transaction AmountLimit
### (1 alice set AmountLimit to 5000000000000
defenseModule.setTransferLimitation, the params is
* AmountLimit
*  5000000000000
![](./img/12.jpg)

### (2 alice try transfer 5000000000001 two times, will be fail. and account be temporarily frozen within 100 blocks
defenseModule.safeTransfer
* BOB
* 5000000000001
![](./img/13.jpg)


### (3 check the tranfer event, there are not TransferSuccess event
![](./img/19.jpg)

## 2 test transaction FrequencyLimit
### (1 bob set FrequencyLimit to less than 2 in 100 blocks
defenseModule.setTransferLimitation, the params is
* FrequencyLimit
* 2
* 100
![](./img/14.jpg)

### (2 bob transfer first time, will be Success
defenseModule.safeTransfer
* ALICE
* 1000000000001
![](./img/15.jpg)

### (3 bob transfer second time, will be Success
defenseModule.safeTransfer
* ALICE
* 1000000000002
![](./img/16.jpg)

### (4 bob transfer three and four time, will be fail, and account be temporarily frozen
defenseModule.safeTransfer
* ALICE
* 1000000000004
![](./img/17.jpg)

### (5 check the tranfer event, only 2 TransferSuccess events
![](./img/18.jpg)

## 3 test freeze account permanent
### (1 charlie free his account 
defenseModule.freezeAccount
* Yes
![](./img/20.jpg)

### (2 charlie try transfer
![](./img/21.jpg)

### (3 check the tranfer event, there are not TransferSuccess event
![](./img/22.jpg)

# pallet-notification
## test notification config
### (1 run localservel
docker run --name notify-serve -p 3030:3030 -d baidang201/notification /notification

### (2 alice set email notification config
notification.setMail
* 116174xxxx@qq.com
* notifytitle
* notifybody
![](./img/26.jpg)


### (3 alice set slack notification config
notification.setSlack
* https://hooks.slack.com/services/xxxxx/xxxxxx/xxxxxxxxxx
* hello,notifymessage

![](./img/27.jpg)

note: you can get webhook url from https://api.slack.com/messaging/webhooks


### (4 alice set discord notification config
notification.setDiscord
* https://discord.com/api/webhooks/xxxxxxx/x-xxxxxxxxxx-xx
* notifybot
* hello,notifycontent  
![](./img/28.jpg)

note: you can get webhook url from https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks


### (5 set AmountLimit to 5000000000000 
defenseModule.setTransferLimitation, the params is
* AmountLimit
*  5000000000000
![](./img/12.jpg)


### (6 trigger fail
defenseModule.safeTransfer
* BOB
* 5000000000001
![](./img/24.jpg)

### (7 check email notification in serve log
docker logs -f notify-serve
![](./img/25.jpg)
![](./img/29.jpg)

### (8 check slack
![](./img/30.jpg)

### (9 check discord
![](./img/31.jpg)

## ref
pallet-notification
* `set_mail` - Configure Email alert methods for users.
* `set_slack` - Configure Slack alert methods for users.
* `set_discord` - Configure Discord alert methods for users.

pallet-defense
* `set_transfer_limit` - The transaction limit and transaction frequency of the user-configured address.
* `set_risk_management` - Freezing time for user-configured addresses.
* `safe_transfer` - Transfer method, check whether the transfer is safe according to user configuration.
* `freeze_account` - Trigger the freezing operation, after the user clicks, the account cannot be transferred within the specified time.

more info: see testcase in https://github.com/redstone-network/redstone-node/blob/main/pallets/defense/src/tests.rs

see api doc in https://redstone-node-doc.vercel.app/pallet_defense/pallet/struct.Pallet.html#