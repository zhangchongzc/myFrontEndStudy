##回调函数理解：
###代码
```javascript
 1. router.get('/question',function (req,res) {
  api.getQuestion(function(questionList){
    questionList = {questionList:questionList};
    console.log('questionList:'+JSON.stringify(questionList));
    res.render('question',questionList)
  })
}); 

2. exports.getQuestion=function (callback) {
  db.getQuestion(function (rows) {
      callback(rows);
  })
}; 

3. exports.getQuestion = function(callback){
    var aContent =  100;
    var sql = "SELECT d.questionId, " +
        "d.questionTitle," +
        "left(d.questionContent,"+ aContent +") as questionContent, " +
        "a.userName as asker, " +
        "d.questionTime, " +
        "COUNT(f.questionId) as answerNumber " +
        "FROM question d JOIN account a ON a.userId = d.userId JOIN answer f on d.questionId = f.questionId " +
        "GROUP BY f.questionId";

    conn.query(sql,function (err,rows) {
        console.log(sql);
        if(err){
            console.log(err);
            return false;
        }else{
            callback(rows);
        }
    })
};
```
###解释
