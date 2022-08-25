## 회원등록 페이지
### join.jsp 
![image](https://user-images.githubusercontent.com/93521099/186085777-b9cfe72c-d7e2-4fc8-8fa1-a3ab853d4aef.png)

```jsp
<!-- DB연결에 필요한 라이브러리 import -->
<%@ page import="DB.DBConnect"%>
<%@ page import="java.sql.*"%>

<%
  String sql = "select max(custno) from member_tbl_02"; // 마지막 번호 검색하는 Query문 작성 후 sql변수에 저장

  Connection conn = DBConnect.getConnection(); // DB 연결
  PreparedStatement pstmt = conn.prepareStatement(sql);
  ResultSet rs = pstmt.executeQuery(); // Query문 실행하여 마지막 번호을 검색하여 변수 rs에 저장

  rs.next();
  int num = rs.getInt(1) + 1; // 마지막 번호에서 1을 더함
%>
```
#### 회원번호를 자동으로 생성해주는 코드
```javascript
function checkValue() {
    if (!document.data.custno.value) {
        alert("회원 번호를 입력하세요");
        data.custno.focus();
        return false;
    } else if (!document.data.custname.value) {
        alert("회원 성명을 입력하세요");
        data.custname.focus();
        return false;
    } else if (!document.data.phone.value) {
        alert("전화 번호를 입력하세요");
        data.phone.focus();
        return false;
    } else if (!document.data.address.value) {
        alert("회원 주소를 입력하세요");
        data.address.focus();
        return false;
    } else if (!document.data.joindate.value) {
        alert("가입 일자를 입력하세요");
        data.joindate.focus();
        return false;
    } else if (!document.data.grade.value) {
        alert("고객 등급을 입력하세요");
        data.grade.focus();
        return false;
    } else if (!document.data.city.value) {
        alert("도시 코드를 입력하세요");
        data.city.focus();
        return false;
    } else {
        return true;
    }
```
#### script를 이용하여 checkValue() 함수를 만들어 줌.
#### checkValue() 함수를 사용하여 각 칸마다 빈칸이 없는지 확인.
#### 위에서부터 빈칸을 확인한 후 빈칸이 있을경우 빈칸에 커서를 잡아 바로 입력할 수 있게 해줌.
#### alert를 이용하여 메세지창을 띄워줌.

![image](https://user-images.githubusercontent.com/93521099/186086062-c0e4608d-eb32-4cc7-8701-d7da498986f2.png)
![image](https://user-images.githubusercontent.com/93521099/186558643-ee788304-1a30-4547-a17c-f616f73534e5.png)
![image](https://user-images.githubusercontent.com/93521099/186086562-f622e0bc-1b31-482b-920b-0d61a31f02f8.png)

#### 모든칸에 입력 한 후 등록 버튼을 누르게되면 join_p.jsp파일이 실행됨.
#### join_p.jsp파일 내에 코드 실행이  member_tbl_02 테이블에 데이터가 추가되면고 모든 작업을 마친 후 index.jsp파일로 화면이 넘어감.


### 사용한 테이블 / 테이블에 넣어준 값
```sql
CREATE TABLE member_tbl_02 (
	custno number(6) NOT NULL PRIMARY KEY,
	custname varchar2(20),
	phone varchar2(13),
	address varchar2(60),
	joindate date,
	grade char(1),
	city char(2)
);

insert into member_tbl_02 values(10001, '김행복', '010-1111-2222', '서울 동대문구 휘경1동', '20151202', 'A', '01');
insert into member_tbl_02 values(10002, '이축복', '010-1111-3333', '서울 동대문구 휘경2동', '20151202', 'B', '01');
insert into member_tbl_02 values(10003, '장믿음', '010-1111-4444', '울릉군 울릉읍 독도1리', '20151202', 'B', '30');
insert into member_tbl_02 values(10004, '최사랑', '010-1111-5555', '울릉군 울릉읍 독도2리', '20151202', 'A', '30');
insert into member_tbl_02 values(10005, '진평화', '010-1111-6666', '제주도 제주시 외나무골', '20151202', 'B', '60');
insert into member_tbl_02 values(10006, '차공단', '010-1111-7777', '제주도 제주시 감나무골', '20151202', 'C', '60');

```
### join_p.jsp (테이블에 데이터 추가해주는 jsp코드)
```jsp
<%
	request.setCharacterEncoding("UTF-8"); // 인코딩 UTF-8로 설정
	String sql = "insert into member_tbl_02 values (?,?,?,?,?,?,?)"; // 저장할 데이터에 해당하는 values 값에 '?'를 입력
	
	Connection conn = DBConnect.getConnection(); // DB 연결
	PreparedStatement pstmt = conn.prepareStatement(sql); 
	
	// 지정해둔 '?' 에 순서에 맞게 데이터를 넣어줌
	pstmt.setInt(1, Integer.parseInt(request.getParameter("custno"))); 
	pstmt.setString(2, request.getParameter("custname"));
	pstmt.setString(3, request.getParameter("phone"));
	pstmt.setString(4, request.getParameter("address"));
	pstmt.setString(5, request.getParameter("joindate"));
	pstmt.setString(6, request.getParameter("grade"));
	pstmt.setString(7, request.getParameter("city"));
	
	pstmt.executeUpdate();
%>
```
#### custno는 데이터형이 number이기 때문에 Int형으로 지정 나머지 컬럼들은 varchar2, char, date이기 때문에 String으로 지정
#### 회원등록 페이지에서 입력칸에 name을 지정하여 지정한 name을 이용하여 입력한 값을 컬럼 순서에 맞게 데이터를 넣어줌.

## 회원목록조회 페이지
### 실행 페이지
![image](https://user-images.githubusercontent.com/93521099/186558854-7063b53b-7bf3-4771-b1ab-b34f9c5fb274.png)

### 코드 
```jsp
<%
	String sql = "select custno, custname, phone, address, " // Query문 작성 후 sql에 저장
		     + "to_char(joindate, 'yyyy-mm-dd') joindate," 
		     + "case when grade = 'A' then 'VIP' when grade = 'B' then '일반' else '직원' end grade, "
		     + "city from member_tbl_02 order by custno asc";
				
	Connection conn = DBConnect.getConnection(); 
	PreparedStatement pstmt = conn.prepareStatement(sql);
	ResultSet rs = pstmt.executeQuery(); // Query문을 실행하여 그 값을 rs변수에 저장
%>
```

```html
<section class="section">
	<h2>홈쇼핑 회원 명단</h2><br>
		<table class="table_line">
			<tr>
				<th>회원번호</th>
				<th>회원성명</th>
				<th>전화번호</th>
				<th>주소</th>
				<th>가입일지</th>
				<th>고객등급</th>
				<th>거주지역</th>
			</tr>
		<% while (rs.next()) { // rs에 값이 있을 경우 실행 %> 
			<tr class="center">
				<td> <%=rs.getString("custno") %> </td>
				<td> <%=rs.getString("custname") %> </td>
				<td> <%=rs.getString("phone") %> </td>
				<td> <%=rs.getString("address") %> </td>
				<td> <%=rs.getString("joindate") %> </td>
				<td> <%=rs.getString("grade") %> </td>
				<td> <%=rs.getString("city") %> </td>
				<td>
					<input type="button" value="수정">
					<input type="button" value="삭제">
				</td>
			</tr>
		<% } %>
	</table>	
</section>
```
#### 회원목록을 조회하기 위해 테이블을 먼저 생성을 해줌
#### tr,th를 이용하여 제목줄을 만들어준 후 while문을 이용하여 데이터를 추가
#### rs변수에 값을 받을 때 String형으로 받기 때문에 getString을 사용
#### while문이 실행 될 때 rs변수가 가지고있는 값만큼 실행되며, rs변수에 들어있는 값을 테이블에 추가

