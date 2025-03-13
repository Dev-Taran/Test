### submission - 1

<pre><code>
function updateSubmissions() {
    CTFd.api.get_submissions_list({ page: 1, per_page: 20 }).then(response => {
        if (!response.success) return;

        var submissions = response.data;

        // correct: true인 데이터만 필터링
        var correctSubmissions = submissions.filter(sub => sub.correct).slice(0, 10);

        var submissionsContainer = document.getElementById("submissions-list");

        // 기존 리스트 비우기
        submissionsContainer.innerHTML = "";

        // 새로 가져온 데이터 추가
        correctSubmissions.forEach(sub => {
            let submissionElement = document.createElement("div");
            submissionElement.innerHTML = `
                <p><strong>${sub.team_name || sub.user_name}</strong>: ${sub.challenge} - 
                <span style="color: green">Correct</span></p>
            `;
            submissionsContainer.appendChild(submissionElement);
        });
    });
}

// 페이지 로드 시 실행
updateSubmissions();

// 1분마다 업데이트
setInterval(updateSubmissions, 60000);
</code></pre>