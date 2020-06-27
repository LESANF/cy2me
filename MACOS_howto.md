# CY2ME
 깔끔하고 안전하게 싸이월드 백업할 수 있는 방법을 공유한다.  
 일반적으로 싸이월드 백업을 프로그램화하게 되면 ID와 비밀번호를 검증되지 않은 프로그램에 입력해야 한다.  
 이는 백업을 하려는 사용자들로 하여금 불안함을 유발할 수 있는 원인이 된다.  
 본 방법은 따로 프로그램화하지 않고, 간단하게 브라우저만으로 백업할 수 있는 솔루션을 제공한다.  
 
 * 다이어리, 게시판, 사진첩, 블로그, 상태 메세지, 2015년 이후 컨텐츠 백업 가능
 * 사진첩은 포스팅한 날짜를 기준으로 Google Photos나 안드로이드 갤러리 등의 사진첩에서 쉽게 관리 가능
 
 **본 페이지에서는 MAC OS에서의 방법을 설명드립니다.**
  * Windows 사용자는 아래 링크를 참조하세요.  
  https://github.com/designe/cy2me
  * 문의 사항은 아래 링크에 댓글로 달아주세요.  
  https://blog.jbear.co/post/cyworld_backup/


 ## 필요한 도구
 - MAC OS : MAC OS 가 설치된 모든 PC에서 가능
 - Chrome Browser : Microsoft Edge, Firefox도 상관없음.


### 아래 순서를 잘 따라하면 쉽게 백업이 가능합니다.


## 1. 싸이월드 접속 

 https://cy.cyworld.com/cyMain  
 싸이월드 계정을 까먹었을꺼다.  
 얼른 아이디/비밀번호부터 찾아서 로그인  
 

## 2. 싸이월드에 홈피 접속
![cyworld2](https://github.com/designe/cy2me/blob/master/assets/cy2.PNG?raw=true)
 
 백업을 하려면 예전의 미니홈피를 접속해야하는데  
 오른쪽 상단에 파란 동그라미 쳐놓은 프로필 이미지를 선택


## 3. Chrome Devtool 실행
![image](https://user-images.githubusercontent.com/1748714/71323798-9ce74100-251a-11ea-9bf7-afb6e926d6f3.png)

 홈피 접속된 상태에서 F12를 눌러보자.  
 Chrome Devtool 이라는게 실행된다. 이 툴은 dock처럼 페이지 바로 옆에 실행이 되거나 독립된 창으로 실행되는데 기본 세팅은 옆에 탭처럼 켜진다. (설정으로 바꿀 수 있음)  
 
 이제 거의 다 왔다.


## 4) Console 탭 선택
아래 스크립트를 세번 빠르게 클릭하면 전체 선택이 가능하다.  
Chrome Devtool의 Console Tab을 선택한 후 복사한 스크립트를 붙여넣고 엔터를 치면 된다.  
 아래 코드는 beta 버전으로 속도와 502, 504 에러에 대응하기 위해 최적화하였습니다.
```js
var last_id,last_dt,tag_value,startdate,enddate,forder_id,airepageno,airecase,airelastdate;var html="";var type="more";var search="";var allMap={};var postIdx=0;var activateReply=true;if(type=="more"){last_id=$(".hiddenId:last").data("id");last_dt="";airepageno=$("#airepageno").val();airecase=$("#airecase").val();airelastdate=$("#airelastdate").val();srchType=$("#searchType").val();tag_value=$("#tagname").val();forder_id=$("#folderid").val()}else{home_idx=0}var backupStartTime=0;var backupEndTime=0;var CY2ME_CATEGORY_INFO={M:{type:"M",title:"Diary",backup_status:"#diary-backup-status"},O:{type:"O",title:"ShareDiary",backup_status:"#share-diary-backup-status"},1:{type:"1",title:"Board",backup_status:"#board-backup-status"},2:{type:"2",title:"Photo",backup_status:"#photo-backup-status"},B:{type:"B",title:"Blog",backup_status:"#blog-backup-status"},P:{type:"P",title:"After2015",backup_status:"#newcontent-backup-status"},T:{type:"T",title:"Status",backup_status:"#status-backup-status"}};function getBase64Image(img){var canvas=document.createElement("canvas");canvas.width=img.width;canvas.height=img.height;var ctx=canvas.getContext("2d");ctx.drawImage(img,0,0);var dataURL=canvas.toDataURL("image/jpg");return dataURL.replace(/^data:image\/(png|jpg);base64,/,"")}function printImageList(){var ret="";var imageCount=0;var allPosts=Object.values(allMap);for(var i=0;i<allPosts.length;i++){if(allPosts[i].type!="2")continue;imageCount++;ret+="http://nthumb.cyworld.com/thumb?v=0&width=810&url="+allPosts[i].image+" "+allPosts[i].date.replace(/\./gi,"")+"_"+allPosts[i].time.replace(/\:/gi,"")+"00."+imageCount+"."+allPosts[i].image.split(".").pop()+" "+allPosts[i].date.replace(/\./gi,":")+" "+allPosts[i].time+"\n"}return ret}function saveAs(filename,file){var a=document.createElement("a"),url=URL.createObjectURL(file);a.href=url;a.download=filename;document.body.appendChild(a);a.click();setTimeout((function(){document.body.removeChild(a);window.URL.revokeObjectURL(url)}),0)}function collectFeeds(t,comment=true){backupStartTime=Date.now();var typeFeed=CY2ME_CATEGORY_INFO[t];activateReply=comment;console.log("Start "+typeFeed.title+" backup :)");$(typeFeed.backup_status+" .backup-message").css("display","none");$(typeFeed.backup_status+" .lds-hourglass").css("display","inline-block");setTimeout((function(){readAllCyPosts(t);var tryCount=0;var totalFeedCount=Object.values(allMap).length;console.log("All "+typeFeed.title+" Feeds Count : "+totalFeedCount);console.log("Start Feeds Backup!");var intervalCtx=setInterval((function(){var finishTrigger=true;var successCnt=0;var failCnt=0;var startCnt=0;var noStartCnt=0;for(var key in allMap){var v=allMap[key];if(v!={}){if(v.isStarted){startCnt++;if(v.isCompleted){successCnt++;continue}else{failCnt++;finishTrigger=false}}else{noStartCnt++}}}if(totalFeedCount==startCnt){Object.values(allMap).some((function(v,i){if(v.isCompleted){return}else{connectCyPost(v.id,v,8e3)}}));tryCount++}else{finishTrigger=false}if(tryCount>10)finishTrigger=true;var hitCal=successCnt/totalFeedCount*100;console.log("Collecting Feed | "+(Date.now()-backupStartTime)+"ms | Eval "+tryCount+" startCnt = "+startCnt+" noStartCnt = "+noStartCnt+" successCnt = "+successCnt+" failCnt = "+failCnt+" | "+hitCal.toFixed(2)+"% ["+successCnt+" / "+totalFeedCount+"] ");if(finishTrigger){clearInterval(intervalCtx);console.log("CY2ME | Backup is going to be finished after 15 seconds. | Thank you");setTimeout((function(){var backupTime=Date.now()-backupStartTime;console.log("총 "+backupTime/1e3+"초 동안 백업이 진행되었습니다.");console.log("Backup Finished.");var allPosts=Object.values(allMap);var file=new Blob([JSON.stringify(allPosts,null,1)],{type:"text/plain;charset=utf-8"});saveAs("MyCy"+typeFeed.title+"_"+Date().replace(/\ /gi,"_").split("_GMT")[0]+".txt",file);$(typeFeed.backup_status+" .lds-hourglass").css("display","none");$(typeFeed.backup_status+" .backup-message").css("display","inline-block")}),15e3)}}),1e4)}),300)}function collectShareDiaries(comment=true){collectFeeds("O",comment)}function collectBoards(comment=true){collectFeeds("1",comment)}function collectBlogs(comment=true){collectFeeds("B",comment)}function collectDiaries(comment=true){collectFeeds("M",comment)}function collectPhotos(comment=true){collectFeeds("2",comment)}function collect2015(comment=true){collectFeeds("P",comment)}function collectStatus(comment=true){collectFeeds("T",comment)}var connectCyPostCnt=0;function connectCyPost(id,post,time=0){try{var ajaxOption={url:"/home/"+homeTid+"/post/"+id+"/layer",cache:false,async:true,dataType:"html",data:{},beforeSend:function(){post.isStarted=true}};if(time!=0)ajaxOption["timeout"]=time;$.ajax(ajaxOption).done((function(viewResult){var output=$("<output>").append($.parseHTML(viewResult));if(typeof $(".textData",output)[0]==="undefined"){post.isCompleted=false;allMap[id]=post;return false}if(post.type!="M"&&post.type!="O")post.title=$("#cyco-post-title",output)[0].innerText.trim();var content="";var imageObj=$("section .cyco-imagelet figure img",output);for(var i=0;i<imageObj.length;i++)content+="<img src ='http://nthumb.cyworld.com/thumb?v=0&width=810&url="+decodeURIComponent(imageObj[i].getAttribute("srctext"))+"'/>";var contentObj=$(".textData",output);for(var i=0;i<contentObj.length;i++)content+=contentObj[i].innerHTML.trim();post.content=content;post.date=$(".view1",output)[0].innerText.trim().split(" ")[0].split("\t").pop();post.time=$(".view1",output)[0].innerText.trim().split(" ")[1];post.isCompleted=true;if(activateReply){var commentCount=post.commentCount;if(commentCount!=0){$.ajax({url:"/home/"+homeTid+"/post/"+id+"/comment",dataType:"json",async:true,data:{}}).done((function(comments){post.comments=[];for(comment_idx in comments.commentList){var temp=comments.commentList[comment_idx].contentModel[0];temp.name=comments.commentList[comment_idx].writer.name;post.comments.push(temp)}allMap[id]=post})).fail((function(){console.log(id+" | Failed | 댓글 수집에 실패하였습니다. 댓글을 제외한 컨텐츠만 저장됩니다.");allMap[id]=post}))}else{allMap[id]=post}}else{allMap[id]=post}})).fail((function(request,status,error){post.isCompleted=false;allMap[id]=post}))}catch(e){console.error(e);console.log("try catch error : "+e)}}function readAllCyPosts(t){allMap={};postIdx=0;last_dt=null;var totalCount=readCyPost(30,t);postIdx=totalCount;if(totalCount>30)postIdx=30;else return;do{readCyPost(totalCount-postIdx,t);postIdx+=30}while(totalCount-postIdx>0);console.log("Analyzation Finishd.")}function readCyPost(cnt,t){var ret=0;$.ajax({url:"/home/"+homeTid+"/posts",data:{startdate:startdate,enddate:enddate,folderid:"",tagname:tag_value,lastid:last_id,lastdate:last_dt,listsize:cnt,homeId:homeTid,airepageno:airepageno,airecase:airecase,airelastdate:airelastdate,searchType:srchType,search:search},cache:false,dataType:"json",async:false,success:function(data){last_dt=data.lastdate;ret=data.totalCount;var baseIdx=postIdx;if(data.postList.length>0){data.postList.some((function(value,index){if(t&&value.serviceType!=t)return false;var post={id:value.identity,type:value.serviceType,writer:value.writer,viewCount:value.viewCount,commentCount:value.commentCount,isStarted:false};allMap[value.identity]=post;switch(post.type){case"2":post.image=value.summaryModel.image;break;case"1":break;case"P":break;case"T":break;case"M":break;case"O":break;case"B":break;case"7":return false}connectCyPost(value.identity,JSON.parse(JSON.stringify(post)));var cal=(baseIdx+index)/ret*100;console.log("Analyzing Feed | "+value.identity+" | "+cal.toFixed(2)+"% ["+(baseIdx+index)+" / "+ret+"] ")}))}else{ret=0}}});return ret}function initializeCy2me(){var css="<style>\n.lds-hourglass { display: none;  position: relative;  width: 22px;  height: 22px; }\n";css+=' .lds-hourglass:after {  content: " ";  display: block;  border-radius: 50%;  width: 0;  height: 0;  margin:6px;  box-sizing: border-box;  border: 10px solid #bbb;  border-color: #bbb transparent #bbb transparent;  animation: lds-hourglass 1.2s infinite;}\n';css+=" @keyframes lds-hourglass {  0% {    transform: rotate(0);    animation-timing-function: cubic-bezier(0.55, 0.055, 0.675, 0.19);  }  50% {    transform: rotate(900deg); animation-timing-function: cubic-bezier(0.215, 0.61, 0.355, 1);  }  100% {    transform: rotate(1800deg);  }}\n";css+=".backup-btn { cursor:pointer; font-size:13px; line-height:25px; color:#777; }\n";css+=".backup-status { display:inline-block; font-weight:normal; color:#fe8536;} \n";css+=".backup-message { display:inline-block; padding-left:5px; display:none;} \n";css+="</style>";$(css).appendTo(document.head);$(".profile dfn:first").html("");var diaryBtn=$("<span class='backup-btn'>").text("다이어리 백업").click(collectDiaries);var diaryStatus=$("<div id='diary-backup-status' class='backup-status'> <div class='lds-hourglass'></div><div class='backup-message'>done</div></span>");var shareDiaryBtn=$("<span class='backup-btn'>").text("공유 다이어리 백업").click(collectShareDiaries);var shareDiaryStatus=$("<div id='share-diary-backup-status' class='backup-status'> <div class='lds-hourglass'></div><div class='backup-message'>done</div></span>");var boardBtn=$("<span class='backup-btn'>").text("게시판 백업").click(collectBoards);var boardStatus=$("<div id='board-backup-status' class='backup-status'><div class='lds-hourglass'></div><div class='backup-message'>done</div></span>");var blogBtn=$("<span class='backup-btn'>").text("블로그 백업").click(collectBlogs);var blogStatus=$("<div id='blog-backup-status' class='backup-status'><div class='lds-hourglass'></div><div class='backup-message'>done</div></span>");var photoBtn=$("<span class='backup-btn'>").text("사진첩 백업").click(collectPhotos);var photoStatus=$("<div id='photo-backup-status' class='backup-status'><div class='lds-hourglass'></div><div class='backup-message'>done</div></span>");var newContentBtn=$("<span class='backup-btn'>").text("2015 이후 백업").click(collect2015);var newContentStatus=$("<div id='newcontent-backup-status' class='backup-status'><div class='lds-hourglass'></div><div class='backup-message'>done</div></span>");var statusBtn=$("<span class='backup-btn'>").text("상태 메세지 백업").click(collectStatus);var statusStatus=$("<div id='status-backup-status' class='backup-status'><div class='lds-hourglass'></div><div class='backup-message'>done</div></span>");$(".profile dfn:first").append(diaryBtn);$(".profile dfn:first").append(diaryStatus);$(".profile dfn:first").append($("<em>"));$(".profile dfn:first").append(shareDiaryBtn);$(".profile dfn:first").append(shareDiaryStatus);$(".profile dfn:first").append($("<em>"));$(".profile dfn:first").append(boardBtn);$(".profile dfn:first").append(boardStatus);$(".profile dfn:first").append($("<em>"));$(".profile dfn:first").append(blogBtn);$(".profile dfn:first").append(blogStatus);$(".profile dfn:first").append($("<br>"));$(".profile dfn:first").append(photoBtn);$(".profile dfn:first").append(photoStatus);$(".profile dfn:first").append($("<em>"));$(".profile dfn:first").append(newContentBtn);$(".profile dfn:first").append(newContentStatus);$(".profile dfn:first").append($("<em>"));$(".profile dfn:first").append(statusBtn);$(".profile dfn:first").append(statusStatus);console.log("CY2ME : Cyworld 백업 준비 완료 | 웹페이지에 보시면 백업 메뉴가 활성화되어 있습니다.")}initializeCy2me();
```

백업 준비 완료되었다는 메세지가 뜰 것이다.
![cyworld8](https://github.com/designe/cy2me/blob/master/assets/cy8.png?raw=true)

## 5) 다이어리, 게시판, 블로그에 쓴 글 백업
홈피의 방문자 기록을 보면 아래와 같이 백업 메뉴가 활성화된 모습을 볼 수 있다.

![image](https://user-images.githubusercontent.com/1748714/71323608-6c9ea300-2518-11ea-9bfc-f3bcf518fdda.png)

남은 일은?  
원하는 버튼을 누르기만 하면 된다 :)

![image](https://user-images.githubusercontent.com/1748714/71323712-a45a1a80-2519-11ea-966c-a1abb6b75fd4.png)

버튼을 누르면 위와 같이 콘솔창에는 현재 상태가 뜨고, Finish가 뜨면 자동으로 백업 파일이 다운로드 된다.  
MyCyDiary_현재시간.txt 파일로 저장이 되어 있다.  브라우저 다운로드 목록을 확인해보자.  


## 5-1) 백업 파일 뷰어로 확인
https://dev.jbear.co/cy2me  
위 링크에 접속해 다운로드 받은 파일을 선택하면 깔끔하게 정리되어 확인할 수 있다.  

다음은 사진 저장 방법이다.  
이건 조금 어려우니 집중해서 따라하길 바란다.


## 6. 사진 전체 다운로드 방법 (MAC OS 에서의 사용법)

시작은 비슷하다. 사진첩 백업 버튼을 클릭해보자.    
수집이 완료되면 MyCyPhotos_현재시간.txt가 다운로드된다.  
일반적으로 MAC OS에서는 아래 위치에 저장될 것이다.
```bash
~/Downloads/MyCyPhotos_***.txt # 사용자 폴더의 Downloads 폴더내 저장
```

이 파일을 https://dev.jbear.co/cy2me 에 접속해 로드한 후,  
"모든 사진 다운로드" 버튼을 누르면 또 다른 텍스트 파일이 받아진다.

terminal 을 실행해보자.  
우선 사진을 저장하고 싶은 폴더를 만든다.  
```bash
mkdir cyphotos
```
만들어진 cyphotos라는 폴더에 다운로드 받은 MyCyPhotos_download_script_blah.txt 텍스트 파일을 이동시킨다.
```bash
cd cyphotos
mv ~/Downloads/MyCyPhotos_download_script_***.txt ./
```

마지막으로 아래의 명령어를 따라치면 싸이월드의 컨텐츠들이 컴퓨터에 저장이 된다.
![cyworld6](https://github.com/designe/cy2me/blob/master/assets/cy6.PNG?raw=true)
```bash
awk '{print $1 " -O " $2}' MyCyPhotos_download_script_***.txt | xargs -n3 wget
```
wget이 없는 경우 위 명령어가 동작안할 수도 있는데,  
그럴땐 아래와 같이 curl이라는 명령어로 대체해서 사용한다.
```bash
awk '{print $1 " -O " $2}' MyCyPhotos_download_script_***.txt | xargs -n3 curl
```


**추가 팁 : 고급 Option**  
exiftool을 쓰면 사진 찍은 날짜를 포스팅 올린 날짜로 일괄 변경해준다.  
https://www.sno.phy.queensu.ca/~phil/exiftool/  
위 사이트에 접속하면 MAC용 exiftool을 받을 수 있고, 관리자 권한으로 실행하여 설치한다.  
아래 명령어로 해당 이미지 폴더에서 실행하시면 끝!  
![cyworld7](https://github.com/designe/cy2me/blob/master/assets/cy7.PNG?raw=true)
```bash
exiftool "-AllDates<filename" *.jpg
```

