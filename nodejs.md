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
这是一个三层的函数回调嵌套机制，回调函数是将一个函数以参数的形式传入到另一个函数中执行。
是在当前函数执行完成之后，再执行回调函数。（因为只有先执行完本函数才能取得数据再传给回调函数）
此处，首先第一层router层中捕获到get请求之后，执行完相应的nodeJs中get方法后，将进入匿名的回调函数中，
匿名回调函数中，首先调用了api.getQuestion()，然后此处调用的api.getQuestion函数中又传入匿名了回调函数，
但是`api.getQuestion`函数首先执行自身，此函数是在api层定义的，所以相当于函数进入了api层执行，此时我们
来看api层中，`exports.getQuestion`，定义的是先执行`db.getQuestion（）`，同时`db.getQuestion`中又有回调函数传入，
但是还是先执行本身，而`db.getQuestion`是在db层定义的，此时相当于函数进入db层执行，此时我们来看db层中
`exports.getQuestion`，通过sql语句操作数据库，取得数据存在变量sql后，通过query方法处理之后，以数组形式保存在rows，
并传给匿名回调函数，在此函数中开始执行callback函数，也就是之前所说的在回调函数`db.getQuestion(function (rows) {
callback(rows);}）`中的匿名函数。此时又进入了api层，执行此函数时，到最后再执行，
```javascript
 api.getQuestion(function(questionList){
    questionList = {questionList:questionList};
    console.log('questionList:'+JSON.stringify(questionList));
    res.render('question',questionList)
  })
  ```
  中的匿名回调函数，最后通过此处的render将页面返回。

