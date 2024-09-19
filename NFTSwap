// SPDX-License-Identifier: MIT  
pragma solidity ^0.8.0;  
  
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";  
import "@openzeppelin/contracts/access/Ownable.sol";  
import "@openzeppelin/contracts/utils/math/SafeMath.sol"; 

  // NFTSwap 合约，继承了 ERC721（用于 NFT 的标准接口）和 Ownable（用于权限管理）
contract NFTSwap is ERC721, Ownable {  
    using SafeMath for uint256;  
  
      // 定义一个 Order 结构体，用于存储挂单信息  
    struct Order {  
        uint256 price;          // 挂单价格  
        address owner;          // 挂单所有者（卖家）  
        bool isActive;          // 挂单是否活跃（是否已被购买或撤销）  
    }  
  
    // 定义一个私有映射，将 tokenId 映射到 Order 结构体  
    mapping(uint256 => Order) private orders;  

    // 定义一个公共常量，表示挂单手续费（这里设置为 0） 
    uint256 public constant LISTING_FEE = 0;  

 // 定义几个事件，用于通知外部系统合约状态的变化  
    event NFTListed(uint256 tokenId, uint256 price);              // NFT 挂单事件  
    event NFTPurchased(uint256 tokenId, address buyer, uint256 price);  // NFT 购买事件  
    event OrderRevoked(uint256 tokenId);                          // 挂单撤销事件  
    event PriceUpdated(uint256 tokenId, uint256 newPrice);        // 价格更新事件  
  
    // 构造函数，设置 ERC721 合约的名称和符号  
    constructor(address initialOwner) ERC721("NFTSwap", "NFS") Ownable(initialOwner) {}
  
    // 卖家挂单函数，外部可调用 
    function listNFT(uint256 tokenId, uint256 price) external {  
        // 要求调用者是 token 的所有者或被批准的人  
        require(_isApprovedOrOwner(msg.sender, tokenId), "Not the owner or approved");  
         // 要求该 tokenId 的挂单当前不活跃（即未被挂单或已被购买/撤销）  
        require(orders[tokenId].isActive == false, "NFT already listed");  
  
        // 将挂单信息存储到 orders 映射中 
        orders[tokenId] = Order({  
            price: price,  
            owner: msg.sender,  
            isActive: true  
        });  
  
        // 触发 NFT 挂单事件  
        emit NFTListed(tokenId, price);  
    }  
  
    // 买家购买函数，外部可调用，且需要支付  
    function purchaseNFT(uint256 tokenId) external payable {  
        // 获取 tokenId 对应的挂单信息  
        Order storage order = orders[tokenId];  
    
        // 要求挂单当前活跃  
        require(order.isActive, "NFT is not listed");  
        // 要求支付的金额与挂单价格一致  
        require(msg.value == order.price, "Incorrect price");  
    
        // 使用 _transfer 函数将 NFT 转移到买家  
        // 转移 NFT 给买家
        // _transfer(order.owner, msg.sender, tokenId);  
    
        // 转移 NFT 给买家  
        _safeTransfer(order.owner,msg.sender, tokenId, ""); 

        // 清除挂单信息  
        // 更新订单状态  
        order.isActive = false;  
        order.price = 0;  
        order.owner = address(0);  
    
        // 触发 NFT 购买事件  
        emit NFTPurchased(tokenId, msg.sender, order.price);  
    }
  
    // 卖家撤单函数，外部可调用  
    function revokeListing(uint256 tokenId) external {   
        // 获取 tokenId 对应的挂单信息  
        Order storage order = orders[tokenId];  
  
        // 要求挂单当前活跃  
        require(order.isActive, "NFT is not listed");  
        // 要求调用者是挂单的所有者  
        require(order.owner == msg.sender, "Not the owner");  
  
        // 清除挂单信息  
        order.isActive = false;  
        order.price = 0;  
        order.owner = address(0);  
  
        // 触发挂单撤销事件  
        emit OrderRevoked(tokenId);  
    }  
  
    // 卖家修改价格函数，外部可调用  
    function updatePrice(uint256 tokenId, uint256 newPrice) external {  
        // 获取 tokenId 对应的挂单信息  
        Order storage order = orders[tokenId];  
  
        // 要求挂单当前活跃  
        require(order.isActive, "NFT is not listed"); 
        // 要求调用者是挂单的所有者   
        require(order.owner == msg.sender, "Not the owner");  
  
        // 更新挂单价格 
        order.price = newPrice;  
  
        // 触发价格更新事件  
        emit PriceUpdated(tokenId, newPrice);  
    }  
  
    // 内部函数，用于检查调用者是否是 token 的所有者或被批准的人  
    function _isApprovedOrOwner(address spender, uint256 tokenId) internal view returns (bool) {  
        // 获取 token 的所有者 
        address owner = ERC721.ownerOf(tokenId);  
        // return (owner == spender || getApproved(tokenId) == spender ||isOwner());  
        // 返回调用者是否是所有者、被批准的人或合约的所有者  
        return (owner == spender || getApproved(tokenId) == spender);  

        // 注意：这里假设 getApproved 函数是从 ERC721 合约中继承或实现的，  
        // 但在标准的 ERC721 合约中并没有这个函数。如果需要使用，您应该在合约中实现它，  
        // 或者确保您的 ERC721 实现包含了这个函数。否则，这里会编译错误。  
        // 同理，_safeTransfer 函数也应该是从 ERC721 合约中继承或实现的，用于安全地转移 NFT。  

    }
}
