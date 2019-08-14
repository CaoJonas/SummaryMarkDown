#### mysql 常见错误

- **Error Code: 1822. Failed to add the foreign key constraint. Missing index for constraint 'fk_card_num' in the referenced table 'bankaccount'**

  > 这是因为所依靠的外键没有自动添加索引， 创建新表时 主键会自动添加索引， 但是其他属性都不会这样， 如果其它标依赖该属性， 就会出现该 属性未添加索引的问题。解决的方式就是为该属性添加索引 

  ```java
  create table bankaccount(
  	account_id bigint primary key auto_increment comment '银行卡编号',
      card_num varchar(16) not null comment '银行卡卡号',	
      password varchar(6) not null comment '银行卡密码',
      balance decimal(19, 2) not null comment '银行账号余额',
      status int not null default 1 comment '银行账户状态'
  );
  create table transactionrecord(
  	record_id bigint primary key auto_increment comment '记录编号',
      card_num varchar(16) not null comment '银行卡密码',
      transaction_date date not null comment '交易时间',
      transaction_amount decimal(19, 2) not null comment '交易金额',
      transaction_type int not null comment '交易类型',
      balance decimal(19, 2) not null comment '交易金额',
      remark varchar(255) comment '交易备注',
      CONSTRAINT fk_card_num foreign key (card_num) REFERENCES bankaccount(card_num)
  );
  ```

  