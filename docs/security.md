// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract AtiAdl.ETH {
    string public name = "Ati-adl.ETH";
    string public symbol = "ATAD";
    uint8 public decimals = 18;
    uint256 public totalSupply;

    mapping(address => uint256) public balanceOf;
    mapping(address => mapping(address => uint256)) public allowance;

    address public owner;
    
    // رویدادها
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
    event Mint(address indexed to, uint256 value);
    event Burn(address indexed from, uint256 value);

    constructor(uint256 _initialSupply) {
        owner = msg.sender; // مالک توکن شما می‌شوید
        totalSupply = _initialSupply * 10 ** uint256(decimals);
        balanceOf[owner] = totalSupply; // تمام توکن‌های اولیه به مالک تخصیص می‌یابد
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Only the owner can perform this action");
        _;
    }

    function transfer(address _to, uint256 _value) public returns (bool success) {
        require(_to != address(0), "Invalid address");
        require(balanceOf[msg.sender] >= _value, "Insufficient balance");

        balanceOf[msg.sender] -= _value;
        balanceOf[_to] += _value;

        emit Transfer(msg.sender, _to, _value);
        return true;
    }

    function approve(address _spender, uint256 _value) public returns (bool success) {
        allowance[msg.sender][_spender] = _value;
        emit Approval(msg.sender, _spender, _value);
        return true;
    }

    function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
        require(_from != address(0), "Invalid address");
        require(balanceOf[_from] >= _value, "Insufficient balance");
        require(allowance[_from][msg.sender] >= _value, "Allowance exceeded");

        balanceOf[_from] -= _value;
        balanceOf[_to] += _value;
        allowance[_from][msg.sender] -= _value;

        emit Transfer(_from, _to, _value);
        return true;
    }

    // تابع Mint برای افزایش تعداد توکن‌ها
    function mint(uint256 _value) public onlyOwner returns (bool success) {
        require(_value > 0, "Minted amount should be greater than zero");
        
        totalSupply += _value * 10 ** uint256(decimals);
        balanceOf[owner] += _value * 10 ** uint256(decimals);
        
        emit Mint(owner, _value * 10 ** uint256(decimals));
        return true;
    }

    // تابع Burn برای کاهش تعداد توکن‌ها
    function burn(uint256 _value) public onlyOwner returns (bool success) {
        require(balanceOf[owner] >= _value * 10 ** uint256(decimals), "Insufficient balance to burn");
        
        totalSupply -= _value * 10 ** uint256(decimals);
        balanceOf[owner] -= _value * 10 ** uint256(decimals);
        
        emit Burn(owner, _value * 10 ** uint256(decimals));
        return true;
    }

    // تابع انتقال مالکیت
    function transferOwnership(address newOwner) public onlyOwner {
        require(newOwner != address(0), "New owner is the zero address");
        owner = newOwner;
    }
}
