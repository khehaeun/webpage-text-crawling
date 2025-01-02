# webpage-text-crawling
// 첫번째 코드를 마지막 화까지 실행 후 마지막 화에서는전용 코드를 추가로 입력해줘야 한다.
//첫 화 
// 일반 회차에서 실행하는 코드
(function() {
    // 텍스트 추출 (HTML 내용 포함)
    let contentElement = document.querySelector('.view-container.view-contents'); // 텍스트 컨테이너
    let htmlContent = contentElement ? contentElement.innerHTML : null;

    if (!htmlContent) {
        console.error("텍스트를 찾을 수 없습니다. CSS 선택자를 확인하세요.");
        return;
    }

    // HTML 태그 제거 및 줄바꿈 유지
    let tempDiv = document.createElement("div");
    tempDiv.innerHTML = htmlContent;
    let chapterText = tempDiv.innerText.replace(/(?:\r\n|\r|\n)/g, '\n').trim();

    // 회차 번호 추출 (URL에서 sortno 파라미터 추출)
    let urlParams = new URLSearchParams(window.location.search);
    let chapterNumber = urlParams.get('sortno') || "알 수 없음";

    // 브라우저 메모리에 텍스트 저장
    window.novelTexts = window.novelTexts || []; // 메모리 초기화
    window.novelTexts.push(`=== ${chapterNumber} ===\n\n${chapterText}`);

    console.log(`회차 ${chapterNumber} 텍스트가 저장되었습니다! 총 ${window.novelTexts.length}개의 회차가 저장됨.`);
})();


// 마지막 회차에서 실행하는 코드
(function() {
    if (!window.novelTexts || window.novelTexts.length === 0) {
        console.error("저장된 텍스트가 없습니다. 각 회차에서 텍스트를 저장하세요.");
        return;
    }

    // 소설 제목과 작가명 추출
    let headerElement = document.querySelector('.vsl-header.vsl-headerFix p'); // 소설 제목
    let writerElement = document.querySelector('.writer span:nth-child(2)'); // 작가명 (두 번째 <span>)
    let novelTitle = headerElement ? headerElement.textContent.trim() : "소설 제목 미상"; // 제목 추출
    let writerName = writerElement ? writerElement.textContent.trim() : "작가명 미상"; // 작가명 추출

    // 파일명 생성
    let fileName = `[${writerName}] ${novelTitle}.txt`;

    // 모든 텍스트를 합치기
    let finalText = window.novelTexts.join('\n\n'); // 모든 회차 텍스트를 하나로 병합

    // 마지막 텍스트 추가
    finalText += `\n\n=== 이 소설의 마지막 화입니다. ===\n\n`;

    // 파일로 저장
    let blob = new Blob([finalText], { type: "text/plain" });
    let link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = fileName;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);

    // 저장 후 메모리 초기화
    window.novelTexts = [];
    console.log(`모든 텍스트가 파일로 저장되었습니다! (파일명: ${fileName})`);
})();
