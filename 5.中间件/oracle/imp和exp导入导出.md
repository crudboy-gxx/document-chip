imp a1cms/a1cms file=ecif.dmp full=y    --imp导入必须有tables或者full=y

exp username/passwd@dbname tables= tablename file=filename.dmp rows=n compress=n搜索     --只导出表结构

exp SDJGZSPT/SDJGZSPT@192.168.1.222:1521/shenlan file=SDJGZSPT20160701.dmp  --full=y导出全库
imp SDJGZSPT_1/SDJGZSPT_1 file=SDJGZSPT20160701.dmp full=y ignore=y

expdp SDJGZSPT/SDJGZSPT@192.168.1.222:1521/shenlan directory=D:\GXX\ dumpfile=wjj_20160701.dmp LOGFILE=wjj_20160701.log

imp newuser/password file=temp.dmp ignore=y fromuser=olduser touser=newuser
