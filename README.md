# weeklyur - Weekly User Report

Manual operation:

# rooaws jpmc/<env>
# roossh roostify-core-ami
# cd roostify-core 
# dotenv rails c

Copy/pasta vars and methods below, according to environment, to produce report delivered 
via email using SupportMailer function. 

This will produce a receipt similar to this. Be aware this is not being sent via postfix
so this will not be logged.

=> #<Mail::Message:47220299166440, Multipart: true, Headers: <Date: Thu, 24 May 2018 18:41:38 +0000>, <From: no-reply@auto-sister-manikin.com>, <To: mortgage_express_IO_Team@chase.com>, <Message-ID: <5b0707623684_7f2d2af248682f58975c0@perf-roostify-core-ip-10-5-202-58.mail>>, <Subject: Chase Perf User Status Report>, <Mime-Version: 1.0>, <Content-Type: multipart/mixed; boundary="--==_mimepart_5b0707621ca9_7f2d2af248682f5897433"; charset=UTF-8>, <Content-Transfer-Encoding: 7bit>>

----

To automate:

- Just one of the SELECT statements below is different (dev: email as Username instead of
  sso_reference_id as Username). Fix this or use conditional? 
- SupportMailer.deliver_csv contains different args per environment

SupportMailer.deliver_csv("Chase Perf User Status Report", "mortgage_express_IO_Team@chase.com", "(chase_perf_user_report)-#{Time.current}", statement.run.to_csv).deliver_now
SupportMailer.deliver_csv("uat.asm User Status Report", "mortgage_express_IO_Team@chase.com", "(uat.asm user report)-#{Time.current}", statement.run.to_csv).deliver_now
SupportMailer.deliver_csv("Chase Production User Status Report", "mortgage_express_IO_Team@chase.com", "(chase_production_user_report)-#{Time.current}", statement.run.to_csv).deliver_now
SupportMailer.deliver_csv("dev.asm User Status Report", "mortgage_express_IO_Team@chase.com", "(New Date dev.asm)-#{Time.current}", statement.run.to_csv).deliver_now


---
perf:

# chase-core-perf
# Send weekly to {{email}}

s = "SELECT sso_reference_id as SID, sso_reference_id as Username, access_control_group_name as Profile, current_sign_in_at.to_date as Last_Activity_Date, created_at.to_date as User_Created_Date, locked_at? as Status FROM users WITHIN #{(Account.all).map(&:token).join(', ')} WHERE borrower? == false WHEN created_at < NOW()"

statement = Roql::Query.load_statement(s, (Account.all).map(&:token))

SupportMailer.deliver_csv("Chase Perf User Status Report", "mortgage_express_IO_Team@chase.com", "(chase_perf_user_report)-#{Time.current}", statement.run.to_csv).deliver_now
---
uat:

# chase-core-uat
# Send weekly to mortgage_express_IO_Team@chase.com

s = "SELECT sso_reference_id as SID, sso_reference_id as Username, access_control_group_name as Profile, current_sign_in_at.to_date as Last_Activity_Date, created_at.to_date as User_Created_Date, locked_at? as Status FROM users WITHIN #{(Account.all).map(&:token).join(', ')} WHERE borrower? == false WHEN created_at < NOW()"

statement = Roql::Query.load_statement(s, (Account.all).map(&:token))

SupportMailer.deliver_csv("uat.asm User Status Report", "mortgage_express_IO_Team@chase.com", "(uat.asm user report)-#{Time.current}", statement.run.to_csv).deliver_now

---
prod:

# chase-core-prod
# Send weekly to {{email}}

s = "SELECT sso_reference_id as SID, sso_reference_id as Username, access_control_group_name as Profile, current_sign_in_at.to_date as Last_Activity_Date, created_at.to_date as User_Created_Date, locked_at? as Status FROM users WITHIN #{(Account.all).map(&:token).join(', ')} WHERE borrower? == false WHEN created_at < NOW()"

statement = Roql::Query.load_statement(s, (Account.all).map(&:token))

SupportMailer.deliver_csv("Chase Production User Status Report", "mortgage_express_IO_Team@chase.com", "(chase_production_user_report)-#{Time.current}", statement.run.to_csv).deliver_now

---
dev:

# chase-core-dev
# Send weekly to mortgage_express_IO_Team@chase.com

s = "SELECT sso_reference_id as SID, email as Username, access_control_group_name as Profile, current_sign_in_at.to_date as Last_Activity_Date, created_at.to_date as User_Created_Date, locked_at? as Status FROM users WITHIN #{(Account.all).map(&:token).join(', ')} WHERE borrower? == false WHEN created_at < NOW()"

statement = Roql::Query.load_statement(s, (Account.all).map(&:token))

SupportMailer.deliver_csv("dev.asm User Status Report", "mortgage_express_IO_Team@chase.com", "(New Date dev.asm)-#{Time.current}", statement.run.to_csv).deliver_now

---


