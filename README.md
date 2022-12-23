# LensProtocol_TRY
LensProtocol 作為web3的社交圖譜底層支援，提供了

### 三大Modules
1. Follow Modules
2. Collect Modules
3. Reference Modules

#### Follow Modules
當用戶想要進行Follow的動作時便會調用此Module，使用者可以隨時自行選擇,修改follow module的方式   
Ex: "fee-on-folow", "suvscription"，都可以客製化在Follow Modules的程式碼中

#### Collect Modules
當用戶進行collect profile的動作時就會呼叫此Function, 所有post都需要附加一個collect module進行互動。   

#### Reference Modules
當一個profile進行與特定profile's publication reference的動作時就會用到此模組, 一但選擇reference對象後便不能修改，但可以選擇不與任何profile's publication進行關聯。   
同樣的也可以自定義相關的規則.  
Ex: 只允許擁有某些NFT的人可comment, follow（用於mirrors, comments的函式可以每個都是分別獨立的規則） 

### 六大功能  
1. Profile  
Profile NFT是 Lens中最核心的關鍵，每個獨立的地址有同擁有多個Profile NFT.  
與其他鏈上身份協議的區別為，Lens Profile可以post Publications, 同時Profile NFT會記錄下所有posts, mirrors, comments及其內此Profile進行的交互紀錄．   

2. Publication(Posts)  
Publication 代表著lens內的所有原創內容包含三種型態: posts, comments, mirrors; Post是最基礎的交互內容，並在其加上comment, mirros額外的功能。  
Publication都是基於用戶的ProfileNFT來發出，同時要注意Publications並不是NFT.  

3. Comment  
Comment基本上可以說是小型的post其屬性也與publication相同, 並同樣由使用者的Profile NFT擁有。  
因為Comment會回覆於特定post下，所以若post有相關reference module規則(僅限關注者進行comment等)，comment也會繼承此規則。  
Comment擁有有Collect模組(自定義如何將其mint成NFT), Reference模組(定義誰可以進行comment, mirror)

4. Mirror  
Mirror的功能就像是reTweet，可以將其他內容進行轉貼分享，並且其屬性也與publication相同。 
但要注意Mirror並沒有ContentURI這個field，因此並無Collect Module也就代表mirror的內容無法被mint成NFT

5. Collect  
當使用者post一個publication出來時，會提供Collect Module的相關設定選項來選擇是否開放其他用戶mint 連接到該publication ContentURI的NFT。   
並且可以自定義Collect的規則Ex: 開放否一段時間內可Mint, 限定的Mint數量。 

6. Follow  
Follow與web2世界的Follow有本質上的不同，當使用者Follow一個用戶時，會得到一個"Follow NFT"的證明。  
其中根據Follow Module的設計，可以自定義不同的玩法，舉例：使用者可設定當其他用戶要Follow時須支付5 MATIC來換取一張"Follow NFT"
並且Follow NFT具有線性增加的ID, 從1開始依序增加
Built-in Governance也有針對Follow NFT進行對應的客製化設計，像是DAO投票時擁有該DAO Follow NFT越久的使用者，其投票比重越大等不同玩法。  

7. Built-In Governance   
使用者可以利用Follow NFT來與社群進行互動，包含投票、委任投票來參與社群的發展。   
而社群(DAO)，則需要由開發者先部署可與Follow NFT串接的合約，並允許擁有Follow NFT的使用者進行議題的提案、表決等功能。  

### Mumbai Testnet API Addresses
官方有部署好與Lens Protocol互動的合約，在mumbai.polygonscan上就可以連接Metamask進行上述提到的六大功能所有操作：[LensHub Proxy (use this one for interactions!)](https://mumbai.polygonscan.com/address/0x60Ae865ee4C725cd04353b5AAb364553f56ceF82)。  
找到Contract Tag下面就可以看到Read, Write Contract; Read, Write as Proxy兩大分類的Contract互動頁籤
Proxy代表的是可升級合約，並且由 Proxy 合約和 Logic (或稱 Implementation)合約組成。使用者的交易是送往 Proxy 合約、和 Proxy 合約互動，Proxy 合約再去問 Logic 合約該怎麼處理使用者的請求。  
接著就來看看跟核心功能有關的合約吧.  

1. Profile    
需Gas Fee   
```function createProfile(DataTypes.CreateProfileData calldata vars)```.  
```function setProfileImageURI(uint256 profileId, string calldata imageURI)```.  
```function setDefaultProfile(uint256 profileId)```.  
```function burn(uint256 tokenId)```.  
```function setFollowModule(uint256 profileId, address followModule, bytes calldata followModuleData) external;```.  
不需Gas Fee   
```function getProfile(uint256 profileId)```.   

2. Publication.  
需Gas Fee   
```function post(DataTypes.PostData calldata vars)```   
不需Gas Fee.  

3. Comment.  
需Gas Fee   
```function comment(DataTypes.CommentData calldata vars)```.  
不需Gas Fee.  

4. Mirror.  
需Gas Fee   
```function mirror(DataTypes.MirrorData calldata vars) external;```.  
不需Gas Fee.  

5. Collect.  
需Gas Fee   
```function collect(uint256 profileId,uint256 pubId, bytes calldata data) ```.  
```function emitCollectNFTTransferEvent(uint256 profileId,uint256 pubId,uint256 collectNFTId,address from,address to)```.  

不需Gas Fee.  
```function getCollectNFT(uint256 profileId, uint256 pubId) external view returns (address);```.  
```function getCollectModule(uint256 profileId, uint256 pubId) external view returns (address);```.  

6. Follow.  
需Gas Fee   
```function emitFollowNFTTransferEvent(uint256 profileId, uint256 followNFTId, address from, address to) external```.  
```function follow(uint256[] calldata profileIds, bytes[] calldata datas) external```.  
```function setFollowNFTURI(uint256 profileId, string calldata followNFTURI) external;```.  
```function setFollowModule(uint256 profileId, address followModule, bytes calldata followModuleData) external;```   
不需Gas Fee.  
```function getFollowNFT(uint256 profileId) external view returns (address);```.  
```function getFollowNFTURI(uint256 profileId) external view returns (string memory);```.  
```function getFollowModule(uint256 profileId) external view returns (address);```.  


