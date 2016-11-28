# two-parse-commit
#概要
  MongoDB数据库中操作single-document总是原子性的，然而，涉及multi-document的操作，而不是原子性的。在这些情况下，使用two-parse-commit，提供这些类型的multi-document更新支持.<br>
#背景
  当执行一个由连续操作组成的事务时，某些问题出现了，比如：

    	原子性：如果一个操作失败，事务内的之前的操作必须 ” 回滚 “到之前的状态（就是 “all or nothing” 里面的 “nothing”）。
    	一致性：如果一个严重的故障（比如网络或者硬件）打断了事务，数据库必须可以恢复到一致的状态。

	对于需要多文档事务的情景，你可以在你的应用里实现两阶段提交以提供这些多文档更新的支持。使用两阶段提交保证数据是一致的，并且在发生错误的情况下，执行事务之前的状态是 recoverable （可恢复的） 。然而，在执行过程中，文档可以展示未确定的（事务提交之前的）数据和状态.
#执行过程
	阶段1：请求阶段（commit-request phase，或称表决阶段，voting phase）

	在请求阶段，协调者将通知事务参与者准备提交或取消事务，然后进入表决过程。在表决过程中，参与者将告知协调者自己的决策：同意（事务参与者本地作业执	行成功）或取消（本地作业执行故障）。

	阶段2：提交阶段（commit phase）

	在该阶段，协调者将基于第一个阶段的投票结果进行决策：提交或取消。当且仅当所有的参与者同意提交事务协调者才通知所有的参与者提交事务，否则协调者将通知所有的参与者取消事务。参与者在接收到协调者发来的消息后将执行响应的操作。<br>
#模型
  假设一个情景，你想从账户 A 转钱到账户 B ，MongoDB里，你可以模仿两阶段提交来达到从 A 账户上减去钱并且为 B 账户添加上钱<br>
	详情参考：http://docs.mongoing.com/manual-zh/tutorial/perform-two-phase-commits.html#pattern<br>

  
