# Gumawa ng pribadong chain na may NEO node

Sa nakaraang pagtuturo, natutunan mo kung paano i-setup at i-deploy ang isang node sa Windows at Linux. Ituturo sa iyo ng tutorial na ito kung paano bumuo ng isang pribadong chain at ang mga hakbang na kinakailangan upang kunin ang NEO at GAS mula sa mga pribadong chain.
 
Ang pag-deploy ng pribadong chain ng NEO ay nangangailangan ng hindi bababa sa apat na server upang maabot ang consensus, kung saan ang bawat server ay tumutugma sa consensus node at dedikadong NEO wallet.

## 1. Configuration sa Virtual machine

Ang pag-deploy sa NEO pribadong chain ay nanganga-ilangan ng apat na server para maabot ang consensus, kung saan ang bawat server tutugma sa consensus node. Para sa layunin na ma ipakita, Gumawa ako ng apat na Windows virtual machine ng Azure, ang sukat ay Standard DS1 v2 (1 core, 3.5 GB RAM). Magagawa mong i-deploy ang pribadong chain sa LAN o virtual machine.

 ![image](/assets/privatechain_1.png)

Pagkatapos gumawa, buksan 10331-10334 port, ang tiyak na pamamaraan para sa sistema sa `firewall` `advanced setting` `inbound rules`, para magtatag ng bagong panuntunan , at e-add ang port 10331-10334.

> [!Tandaan]
> Kung lilikha ka ng virtual machine sa cloud server, mag log in sa virtual machine's administrative background, 
mag-set up ng network security group 
>
> Azure setup method ay: `network interface` `network security group` `inbound security rules` `add` add port 10331-10334.

Kapag na likha na ang virtual machine, i-save ang IP addresses sa apat na virtual machines para magamit kalaunan.

## 2. Instulasyon ng NEO node

Ang proseso ng pag-install sa NEO node ay inilarawan sa mga detalye sa itaas. Tingnan dito ang instraksyon ng pag-install (setup.md). 

## 3. Gumawa ng wallet 

Una, may ginawa na tayong apat na wallet files, ito ay ang wallet1.db3 - wallet4.db3. Dito pwede e-execute sa wallet sa bersyon ng PC at sa command line wallet, kung saan ang sumusunod na figure ay screenshot ng command line client.
  
![image](/assets/privatechain_3.png)

Kapag nagawa na ang wallet at ang na i-save na tugmang public key, (i.e. saved to a txt file), direktang kopyahin ang public key o kaya gamitin ang `list key` i-command sa [CLI Command](cli.md) para makita ang public key, at kopyahin ito.
       
Pagkatapos, kopyahin ang apat na wallet patungo sa apat na virtual machine node na direktoryo.

## 4. Baguhin ang configuration file ng node 

Buksan ang configuration file ng node `protocol.json`. 

First modify the `Magic` value. Magic is used to identify the source network of the message, and specifying a different Magic ensures that different network information in the NEO block are not sent to other networks, during transmission.
Una baguhin ang halaga ng `Magic`.Ang Magic ay ginagamit upang ma kilala ang pinagmulang network ng mensahe, at kung iba ang Magic 
> [!Tandaan]
> Ang uri ng Magic ay unit, tandaan na ang halaga na iyong ilalagay ay magsisimula sa [0 - 4294967295].

Baguhin ang `StandbyValidators`, at ilagay dito ang apat na public keys na ine-record sa pangatlong hakbang.

Panghuli baguhin ang `SeedList` ilagay ang IP address na na-irekord  sa una hakbang, at ang port number. Halimbawa, ang sumusunod na configuration ay aking binago..

```json
{
  "ProtocolConfiguration": {
    "Magic": 1704630,
    "AddressVersion": 23,
    "StandbyValidators": [
"02f27545181beb8f528d13bbb66d279db996ecb56ed9a324496d114acb48aa7a32",
      "02daa386d979ae6643869a365294055546023acb332ee1a74a5ae5d54774a97bac",
      "0306f12f7217569cdbe9dde9ff702d0040e0a4570873eee63291adaa658128e55c",
      "035781b4d55dc58187f61b5d9277afbaae425deacc5df57f9891f3a5c73ecb24df"
   ],
    "SeedList": [
      "13.75.112.62:10333",
      "137.116.173.200:10333",
      "168.63.206.73:10333",
      "137.116.171.134:10333"
   ],
    "SystemFee": {
      "EnrollmentTransaction": 0,
      "IssueTransaction": 0,
      "PublishTransaction": 0,
      "RegisterTransaction": 0
    }
  }
}
```

SystemFee is the system fee, the current fee is as follows (in units of GAS):

Registration for book-keepers - 1000, Distribution of Assets - 500 Smart Contracts 500, Registering Assets - 10000

You can set the system fee for your private chain here.

Finally, modify the modified `protocol.json` to the 4-node client directory, replacing the previous protocol.json file.

Then in the four virtual machines, enter the following commands to start the node, open the wallet, and begin the consensus process. Please refer to the command [CLI Command Reference](cli.md), if you are unsure.

Start node:

`Dotnet neo-cli.dll`

Open wallet:

`Open wallet wallet1.db3`

Note: Not all nodes are to open wallet1, each node should open its own corresponding wallet file.

`Start consensus`

If the above operation is successful, then the four nodes will begin a consensus process, as shown

![image](/assets/privatechain_8.png)

4 nodes can still achieve consensus, even if one machine is turned off, as shown:

![image](/assets/privatechain_9.png)



## 5. Extracting NEO and GAS

Install the PC version of the client (Neo-GUI), modify the configuration file protocol.json to connect to the private chain.

Open the wallet. If the lower left corner of the connection number is non-zero, and has been synchronized to the same block, it means that the client has been successfully connected to the private chain.

Open the wallet wallet1.db3 in the PC client, add the multi-party signature address, enter the four public keys in protocol.json, set the minimum number of signatures to 3 (the number of consensus nodes/2 + 1), as shown.

![image](/assets/privatechain_12.png)

Click OK. In order to rebuild the wallet index, click on the 'wallet' in the menu bar, and then you will see the contract address has 100 million NEO shares.

![image](/assets/privatechain_14.png)

> [!Note]
> All 4 wallets have to perform the operation of adding multi-party signature address, and rebuilding of the wallet index.

Here we want to send the NEO from the contract address to the normal address. To do so, open any of the four wallet, click `transaction`, `transfer` and enter the recipient address, in order to transfer 100 million NEO to this recipient address.

The system will then notify you that there have not been enough signatures to complete the transaction. Copy the code to your clipboard, open the second wallet, click `transaction`, `signature` and paste the code that you have just copied. Click `signature` and copy the code. Open the third wallet, click `transaction`, `signature` and paste the code that you have just copied. Click the `signature` button. At this time you will notice a pop-up window that displays a `broadcast` button in the lower left corner, which means all the signatures required to send the transaction have been collected. Click `broadcast`. Once the transfer transaction begins broadcasting it will take about 15 seconds for successful remittance to the account.

![image](/assets/privatechain_20.png)

The operation to extract the GAS is also similar. Click `Advanced`, `Claim GAS`, `Claim` as shown. Remember the wallet address, you will need it later.

![image](/assets/privatechain_21.png)

The next operation is similar to the transfer of the NEO. Copy down the code that has insufficient signatures, open the second wallet, click `transaction`, `signature`, and paste the code that you have just copied. Click `signature`, and copy the code down. Open the third wallet, click on the `transaction`, `signature` and paste the code that you have just copied. Click `signature` and then `broadcast` to broadcast the transaction to claim your GAS. Once the claim transaction begins broadcasting it will take about 15 seconds for successful remittance to the account.

After successful extraction the GAS will enter the first standard address of the wallet (i.e. the top wallet address) where you initiated the extraction of the GAS, as shown.

![image](/assets/privatechain_26.png)
