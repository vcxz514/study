## Spring Project 구조
#

![image](https://user-images.githubusercontent.com/61821641/167744740-a1d466e5-d608-46a2-8ab9-340bfcff668c.png)

#
## JAVA

![image](https://user-images.githubusercontent.com/61821641/167518834-27d24685-aebd-4862-b545-03859ea8467d.png)

```
localhost:8080/board/list
```
Controller에서 url을 매핑해준다. 그리고 서비스를 통해 뷰에 뿌려줄 data를 가져온다. 

![image](https://user-images.githubusercontent.com/61821641/167744927-3ae70a00-2e66-4600-9b2b-245c7a0a13a9.png)

DB작업이나 API 작업은 Repository에서 하고 Service에선 Repository에서 데이터를 받아 데이터 타입에 맞게 넣어준다. 
![image](https://user-images.githubusercontent.com/61821641/167745009-8493e0e5-1591-45d0-a71c-cf3022e13375.png)
- 읽기 전용 트랜잭션 사용 > 속도 최적화
```
@Transactional(readOnly=true)
```
- 해당 옵션을 주면 Spring Framework가 Hibernate Session의 Flush 모드를 Manual로 설정하여, 강제로 flush()를 하지 않는 한 flush()가 일어나지 않는다.
    - 트랜잭션이 커밋되어도 플러시되지 않음
    - flush() 할 때 일어나는 스냅샷 비교와 같은 무거운 로직을 수행하지 않음 > 성능 향상
    - 트랜잭션 시작, 로직수행, 커밋을 똑같이 동작함

Repository에서 DB작업을 하는데 JpaRepository에 내장된 findAll이나 findById를 쓸 수도 있고 쿼리문을 직접 작성할 수도 있다.
![image](https://user-images.githubusercontent.com/61821641/167746263-9bc250b2-4e32-4a26-8e4c-19a275f47122.png)

board model이다

![image](https://user-images.githubusercontent.com/61821641/167746719-31e934a5-c115-4bc7-83c9-53b2fe90cbe7.png)

board DB작업에서 요청과 응답 모델이다.

![image](https://user-images.githubusercontent.com/61821641/167746768-7b2df2fe-93ab-44c7-9b4b-a6e10aa86207.png)

![image](https://user-images.githubusercontent.com/61821641/167746941-71177994-87e6-4412-b356-8233de4d3dcd.png)
#

build.gradle에서 의존성을 추가하면 jar 파일이 생긴다.

![image](https://user-images.githubusercontent.com/61821641/167518564-164f1aa1-cda7-488c-8bd0-0c28ae0b4689.png)

![image](https://user-images.githubusercontent.com/61821641/167518715-2210fa41-9aeb-492a-a5cd-477542b8f0e0.png)

![image](https://user-images.githubusercontent.com/61821641/167518769-9b2498a3-d4cb-440f-97c5-5f789bd0e996.png)
#
## VIEW 
![image](https://user-images.githubusercontent.com/61821641/167747528-5b4a2804-edaf-4667-9559-a7a44798a2c9.png)

templates/board/list.html
```
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout" layout:decorate="~{layout/default_layout}">
   	<!-- layout Content -->
	<th:block layout:fragment="content">
		<div id="wrapper">
			<div class="container">
		    	<form action="/board/list" id="frm" method="get">
				    <div class="col-md-12 mt-4">
				    	<button type="button" class="btn btn-danger" onclick="fnDelete()">Delete</button>
				        <button type="button" class="btn btn-primary" onclick="javascript:location.href='/board/write'">Register</button>
				        <div class="float-right">
				        	<button type="button" class="btn btn-primary" onclick="javascript:location.href='/logout'">Logout</button>	
				        </div>
				        <table class="table table-striped table-horizontal table-bordered mt-3">
				            <thead class="thead-strong">
				                <tr>
				                    <th width="5%"><input type="checkbox" id="chkAll"></th>
				                    <th width="10%">No.</th>
				                    <th width="">Title.</th>
				                    <th width="20%">Writer.</th>
				                    <th width="10%">Views.</th>
				                    <th width="20%">Reg Time.</th>
				                </tr>
				            </thead>
				            <tbody id="tbody">
				            	<tr th:each="list,index : ${resultMap.list}" th:with="paging=${resultMap.paging}">
									<td>
										<input type="checkbox" name="deleteId" th:value="${list.id}">
									</td>
									<td>
										<span th:text="${(resultMap.totalCnt - index.index) - (paging.pageNumber * paging.pageSize)}"></span>
									</td>
									<td>
										<a th:href="@{./view(id=${list.id})}"><span th:text="${list.title}"></span></a>
									</td>
									<td>
										<span th:text="${list.registerId}"></span>
									</td>
									<td>
										<span th:text="${list.readCnt}"></span>
									</td>
									<td>
										<span th:text="${list.registerTime}"></span>
									</td>
								<tr>
				            </tbody>
				        </table>
				        <div class="row">
							<div class="col">
								<ul class="pagination">
									<li class="page-item" th:each="index : ${#numbers.sequence(1, resultMap.totalPage)}" th:with="paging=${resultMap.paging}">
										<a class="page-link" th:classappend="${paging.pageNumber ==  (index-1)} ? bg-primary : bg-secondary" th:href="@{./list(page=${index - 1},page=${paging.pageSize})}">
											<span class="text-white" th:text="${index}"></span>
										</a>
									</li>
								</ul>
							</div>
						</div>
				    </div>
			    </form>
		    </div>
	    </div>
	    <script th:inline="javascript">
	    	// header checkbox event
	    	$("#chkAll").click(function() {
	    		if (this.checked) {
	    			$("input[name='deleteId']").prop("checked", true);
	    		} else {
	    			$("input[name='deleteId']").prop("checked", false);
	    		}
	    	});
	    	
	    	// body checkbox event
	    	$("input[name='deleteId']").click(function() {
	    		let delInpLen = $("input[name='deleteId']").length;
	    		let delInpChkLen = $("input[name='deleteId']:checked").length;
	    		
	    		if (delInpLen == delInpChkLen) {
	    			$("#chkAll").prop("checked", true);
	    		} else {
	    			$("#chkAll").prop("checked", false);
	    		}
	    	});
	    
			function fnDelete() {
				let delInpChkLen = $("input[name='deleteId']:checked").length;
				
				if (delInpChkLen > 0) {
					if (confirm("Do you want to delete it?")) {
						let frm = $("#frm");
						frm.attr("action","/board/delete");
						frm.attr("method","post");
						frm.submit();
					}
				} else {
					alert("Not selected.");
				}
			}
		</script>
	</th:block>
</html>
```
templates/board/view.html
```
<!DOCTYPE html>

<html xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout" layout:decorate="~{layout/default_layout}">
   	<!-- layout Content -->
	<th:block layout:fragment="content">
		<div class="container">
			<h1>Board View.</h1>
			<form id="frm" action="/board/view/action" method="post" th:with="info=${resultMap}" enctype="multipart/form-data">
				<input type="hidden" name="id" th:value="${info.id}">
				<div class="mb-3">
				    <label class="form-label font-weight-bold"><span class="text-danger">* </span>Title.</label>
				    <input type="text" class="form-control" name="title" th:value="${info.title}" required >
				</div>
				<div class="mb-3">
			    	<label class="form-label font-weight-bold"><span class="text-danger">* </span>Content.</label>
			    	<textarea class="form-control" rows="5" name="content" th:text="${info.content}" required ></textarea>
				</div>
				<div class="mb-3">
				    <label class="form-label font-weight-bold"><span class="text-danger">* </span>Writer.</label>
				    <input type="text" class="form-control" name="registerId" th:value="${info.registerId}" required >
				</div>
				<div class="clearfix mt-5">
					<div class="">
						<button type="button" class="btn btn-success" onclick="javascript:location.href='/board/list'">Previous</button>
						<button type="button" class="btn btn-primary" onclick="fnSubmit();">Edit</button>
						<button type="button" class="btn btn-danger" th:onclick="fnViewDelete()">Delete</button>
					</div>
				</div>
			</form>
		</div>
		<!--jQuery 추가 -->
<script src="https://code.jquery.com/jquery-1.12.4.min.js"></script>
		<script th:inline="javascript">
			let frm = $("#frm");
			
			function fnSubmit() {
				frm.submit();
		    }
		    
		    $(function() {
		    	
		    	frm.validate({
		            messages: {
		            	// Message Custom..
		            	title: {
		                    required : "Custom required, Please enter a title."
		                }
		            },
		            submitHandler: function (form) {
		                // Submit Action..
		                form.submit();
		            }
		        });
		    });
		</script>
		
	</th:block>
	
</html>
```
templates/board/write.html
```
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout" layout:decorate="~{layout/default_layout}">
   	<!-- layout Content -->
	<th:block layout:fragment="content">
		<div class="container">
			<h1>Board Register.</h1>
			<form id="frm" action="/board/write/action" method="post" enctype="multipart/form-data">
				<div class="mb-3">
				    <label class="form-label font-weight-bold"><span class="text-danger">* </span>Title.</label>
				    <input type="text" class="form-control" name="title" required >
				</div>
				<div class="mb-3">
			    	<label class="form-label font-weight-bold"><span class="text-danger">* </span>Content.</label>
			    	<textarea class="form-control" rows="5" name="content" required ></textarea>
				</div>
				<div class="mb-3">
				    <label class="form-label font-weight-bold"><span class="text-danger">* </span>Writer.</label>
				    <input type="text" class="form-control" name="registerId" required >
				</div>
				<div class="mt-5">
					<button type="button" class="btn btn-success" onclick="javascript:location.href='/board/list'">Previous</button>
					<button type="button" class="btn btn-primary" onclick="fnSubmit();">Submit</button>
				</div>
			</form>
		</div>
		<script th:inline="javascript">
			let frm = $("#frm");
			
			function fnSubmit() {
				frm.submit();
		    }
		    
		    $(function() {
		    	
		    	frm.validate({
		            messages: {
		            	// Message Custom..
		            	title: {
		                    required : "Custom required, Please enter a title."
		                }
		            },
		            submitHandler: function (form) {
		                // Submit Action..
		                form.submit();
		            }
		        });
		    });
		</script>
	</th:block>
</html>
```

레이아웃 적용하기

templates/layout/default_layout.html
```
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"> 
	<th:block th:replace="fragments/header :: headerFragment"></th:block>
	<body>
		<th:block layout:fragment="content"></th:block>
	</body> 
	<th:block th:replace="fragments/footer :: footerFragment"></th:block>
</html>
```

templates/fragments/header.html
```
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" >
	<th:block th:fragment="headerFragment">
		<head>
			<title>승주의 게시판</title>
			<meta charset="UTF-8">
			<meta name="viewport" content="width=device-width, initial-scale=1" />
			<!-- 게시판 css 추가 -->
		    <link rel="stylesheet" href="/css/board.css">
			<!--부트스트랩 css 추가 -->
		    <link rel="stylesheet" href="/css/lib/bootstrap.min.css">
		    <!--jQuery 추가 -->
		    <script src="/js/lib/jquery.min.js"></script>
		    <!--부트스트랩 script 추가 -->
    		<script src="/js/lib/bootstrap.min.js"></script>
    		<!-- jQuery Validation Plugin 추가 -->
			<script src="https://cdn.jsdelivr.net/npm/jquery-validation@1.19.3/dist/jquery.validate.min.js"></script>
		</head>
    </th:block>
</html>
```

templates/fragments/footer.html
```
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
	<th:block th:fragment="footerFragment">
		<div class="card mt-5">
			<div class="card-body text-center">
				<p class="card-text"><small class="text-muted">Copyright © Venh.log Tistory Corp. All Rights Reserved.</small></p>
			</div>
		</div>
	</th:block>
</html>
```

templates/board/view.html
```
<html xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout" layout:decorate="~{layout/default_layout}">
```

#
## DB

application.properties 파일 DB 설정
```
spring.datasource.driverClassName=org.mariadb.jdbc.Driver
spring.datasource.url=jdbc:mariadb://localhost:3306/board_study
spring.datasource.username=root
spring.datasource.password=zmwpxm98:D
```
DB 생성
```
CREATE TABLE IF NOT EXISTS `board`(
`id`bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'PK',
`title` varchar(200) NOT NULL COMMENT '제목',
`content` text NOT NULL COMMENT '내용',
`read_cnt` int(11) NOT NULL DEFAULT 0 COMMENT '조회수',
`register_id` VARCHAR(100) NOT NULL COMMENT '작성자',
`register_time` DATETIME NULL DEFAULT NULL COMMENT '작성일',
`update_time` DATETIME NULL DEFAULT NULL COMMENT '수정일',
PRIMARY KEY(`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3 COMMENT='게시판';
```

#


완성된 UI

![image](https://user-images.githubusercontent.com/61821641/167747274-d3548a9a-b6e7-4afb-b098-5b90b57589ac.png)

DB에도 잘 들어온다.

![image](https://user-images.githubusercontent.com/61821641/167747325-f565b960-6b80-410b-a450-a8fd64255562.png)
#

application.properties 재시작 없이 HTML 적용
```
spring.thymeleaf.cache=false
```
application.properties 재시작 없이 정적 소스 반영
```
spring.devtools.livereload.enabled=true
spring.freemarker.cache=false
spring.thymeleaf.cache=false
```
build.gradle에 devtools 추가
```
implementation 'org.springframework.boot:spring-boot-devtools'
```
#
## References

- [Spring Boot](https://kitty-geno.tistory.com/124?category=983148)
- [JPA](https://happyer16.tistory.com/entry/JPA-15%EC%9E%A5-%EA%B3%A0%EA%B8%89-%EC%A3%BC%EC%A0%9C%EC%99%80-%EC%84%B1%EB%8A%A5-%EC%B5%9C%EC%A0%81%ED%99%94)


