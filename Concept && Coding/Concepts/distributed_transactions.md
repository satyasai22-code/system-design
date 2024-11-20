# Distributed Transactions

- Transaction: Set of operations which need to be performed or simply say group of task

- ACID Properties: Atomicity, Consistency, Isolation and Durability
   - Atomicity: All or nothing (2000 debit, 2000 credit)
   - Consistency: Data must be in consistent state before and after transaction (x + 2000 rs, x - 2000 rs, Total balance still be 2x)
   - Isolation: Transaction should not be affected by other transaction (More than one transaction that is runnnig concurrently, appears to be serialized). Each DB operation is locked until it is completed.
   - Durability: Transaction should be permanent, even if DB fails (After transaction is committed, it should not be rolled back)

Begin Txn:
 - Withdraw 1000 from A (Put lock on A record in DB)
 - Deposit 1000 to B (Put lock on B record in DB)

    if All is success:
    
        Do Commit
    
    else:
    
        Do Rollback

- For one DB it is great. But, how to handle in distributed system, where operations involved multiple DBs or As Transaction is local to particulr database, how we will satisfy the transaction property in distributed system?

- Take above example in Distributed Inventory System. We have two DBs, one for Inventory and other for Orders.
- Update Order DB (Update order DB to 1. Here txn is local, it is fine and persisted)
- Update Inventory DB (Tried to update but somehow failed. Here txn is local, so rollback happens within Inventory DB but not in Order DB)
- When set of operations includes multiple DBs, this doesn't work. Transactions are local to particular DB. Each DB has its own transaction manager.

How to handle this?

 - Two Phase Commit (2PC) (Very popular)
 - Three Phase Commit (3PC) (Not much used coz of complexity)
 - SAGA Pattern (still used very much)

## Two Phase Commit (2PC)
 - As name says, there are 2 phases in this protocol:
   - Voting or Prepare Phase
   - Decision or Commit phase
 - We need something called Transaction Coordinator (TC) to coordinate the transaction.

 Application (gives respective operations to be done)-> TC(starts transaction) - Order Microservice
                                                                               \ Inventory Microservice

  Now all operations(say update queries) are done(DB puts a lock and updates) and changed in the DB but not COMMITED                                                                      
  Phase 1 begins:
  TC asks all participants (Order MS, Inventory MS), whether they are all PREPARED for committing the changes   

  Phase 2 begins:
   If all particapants gives OK to TC, now TC takes a decision and sends a commit to all participants.   


  Start transaction -> Do all the operations (But dont commit) -> TC asks all participants to prepare(Phase 1 begins)-> TC asks all participants to commit (Phase 2 Begins)-> If all OK, commit(all participants will commit) else rollback.

  If any step is failed, say one of Operations(update inventory) step is failed, then In Phase1, Inventory service give NO response. If any participants say NO, then TC will abort the transaction.

- Potential Issues:
   - TC can fail
   - Participants(Order MS, Inventory MS) can fail
   - Coz of above, prepare message can be lost(Coz of TC failure)
   - OK msg can be lost(Coz of participants failure)
   - Either Commit or abort message is lost(Coz of TC failure)

- Log file(Permanent Storage), TC write to the Log file before sending it to participants. Vice versa participants write to the log file before sending it to TC. With this services/TC can see from where they should restart.
- Lets take `Prepare message can be lost`. Inventory already put a lock on transaction and completed operation.Waiting from TC for prepare msg.But its prepare message is lost coz TC failure. Inventory will assume some prblm with TC and will abort the transaction. Now TC restarts and sends a Prepare msg but Inventory sends NO and TC aborts the transaction.
- Lets take `OK message is lost`. TC sent prepare msg and waiting for participants to send OK message. Order sends OK but inventory(is down) sends nothing. After timeout TC will abort the transaction. Now Inventory MS comes up and asks TC regarding txn(Inventory reads log and takes latest). Now TC sends that txn is aborted. Now Inventory MS will abort the transaction.
- Lets take `Commit message is lost`(very imp). Either TC is down or message got lost, what should participants do? Also some participants might have recieved commit msg. Participant is blocked now. It waits for TC to come up. TC checks the log file and send the decision. 
- The problem with above point is participants cant take any decision. They are blocked and waiting for TC to come up. This is where 3PC comes in.

## Three Phase Commit (3PC) 
- Non blocking Protocol
- 3 Phases:
    - Prepare Phase (Same as 2PC)
    - Pre-Commit Phase (Phase 2)
    - Commit Phase
 
Order:
   - Start Transaction. DBs are locked and updates are done(but not committed).
   - Phase 1: Same as 2PC
   - Phase 2 begins: In the log file, TC writes a decision to commit or abort based on the prepare message(phase 1). Very Imp to note that this is to only share info but not instructing to commit or abort.
- Now Lets discuss the scenarios we have done for 2PC
- TC sent prepared msg for all participants and TC got OK from all. Pre commit phase is also done. After that TC has failed(after Phase 2). But participants are not blocked. They check the decision from TC from phase-2(pre-commit) and   