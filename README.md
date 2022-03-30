# Moonbeam


// SPDX-License-Identifier: CC-BY-SA-4.0

// Version of Solidity compiler this program was written for
pragma solidity 0.6.4;

contract PoorStaking {
    // Accept any incoming amount
    receive() external payable {}

    address owner = msg.sender;
    uint256 private _entryFee;
    uint256 private _feeSum;
    uint256 RoundDuration = 1800;
    uint256 MinimumDelegationPerCandidate = 50 * 10**18;
    uint256 MaximumDelegatorsPerCandidate = 300;	
    uint256 MaximumDelegations = 300;	
    uint256 RewardPayoutDelay = 3600;
    uint256 AddIncreaseDelegation = 1800;
    uint256 DecreaseDelegationDelay = 1800;
    uint256 RevokeDelegationsDelay = 1800;
    uint256 LeaveDelegatorsDelay = 1800;

    enum CROWLAND_STATE {
        OPEN,
        CLOSED
    }
    CROWLAND_STATE public crowland_state;

    //Default Validator
    address public defaultCollator;

    // It will represent a single delegator.
    struct Delegator {
        uint256 amount; // weight is accumulated by delegation   TODO check fo MAX data (buffer overflow)
        address delegatorAddress;
        uint256 currentAmountInStaking; // weight is accumulated by delegation   TODO check fo MAX data (buffer overflow)
        uint blockNumber;
        uint roundNumber;
    }

    // Access control modifier
    modifier onlyOwner {
        require(msg.sender == owner, "Only the contract owner can call this function");
        _;
    }

    // This declares a state variable
    mapping(address => Delegator) public delegators;

    function enter() public payable {
        require(crowland_state == CROWLAND_STATE.OPEN);
        uint256 currentFee = getEntranceFee();
        require(msg.value  >= currentFee, "Not enough GLRM!");

        _feeSum += currentFee;

        Delegator storage delegator = delegators[msg.sender];
        delegator.amount += msg.value - currentFee;
        delegator.delegatorAddress = msg.sender;
        delegator.blockNumber = block.number;
        delegator.roundNumber = block.number / RoundDuration; //Approssimare per difetto
    }


    function getEntranceFee() public view returns (uint256) {
        uint256 adjustedPrice = _entryFee * 10**18; // 18 decimals
        return adjustedPrice;
    }


    //Owner Setters
    function setCrowlandClosed() public onlyOwner {
        crowland_state = CROWLAND_STATE.CLOSED;
    }

    function setCrowlandOpen() public onlyOwner {
        crowland_state = CROWLAND_STATE.OPEN;
    }

    function setEntranceFee(uint256 entryFee) public onlyOwner {
        _entryFee = entryFee;
    }

    function setRoundDuration(uint256 roundDuration) public onlyOwner {
        RoundDuration = roundDuration;
    }

    function setMinimumDelegationPerCandidate(uint256 minimumDelegationPerCandidate) public onlyOwner {
        MinimumDelegationPerCandidate = minimumDelegationPerCandidate;
    }

    function setMaximumDelegatorsPerCandidate(uint256 maximumDelegatorsPerCandidate) public onlyOwner {
        MaximumDelegatorsPerCandidate = maximumDelegatorsPerCandidate;
    }

    function setMaximumDelegations(uint256 maximumDelegations) public onlyOwner {
        MaximumDelegations = maximumDelegations;
    }

    function setRewardPayoutDelay(uint256 rewardPayoutDelay) public onlyOwner {
        RewardPayoutDelay = rewardPayoutDelay;
    }

    function setAddIncreaseDelegation(uint256 addIncreaseDelegation) public onlyOwner {
        AddIncreaseDelegation = addIncreaseDelegation;
    }

    function setDecreaseDelegationDelay(uint256 decreaseDelegationDelay) public onlyOwner {
        DecreaseDelegationDelay = decreaseDelegationDelay;
    }

    function setRevokeDelegationsDelay(uint256 revokeDelegationsDelay) public onlyOwner {
        RevokeDelegationsDelay = revokeDelegationsDelay;
    }

    function setLeaveDelegatorsDelay(uint256 leaveDelegatorsDelay) public onlyOwner {
        LeaveDelegatorsDelay = leaveDelegatorsDelay;
    }
    
}
