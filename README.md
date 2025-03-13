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

---

### submission - 2
<pre><code>
function updateSubmissions() {
    CTFd.api.get_submissions_list({ page: 1, per_page: 20 }).then(response => {
        if (!response.success) return;
        
        var submissions = response.data;

        // correct만 필터링 후 최신 순 정렬
        var correctSubmissions = submissions
            .filter(sub => sub.type === "correct")
            .sort((a, b) => new Date(b.date) - new Date(a.date)) // 최신순 정렬
            .slice(0, 10); // 최근 10개만 가져오기

        var submissionsContainer = document.getElementById("submissions-list");
        submissionsContainer.innerHTML = ""; // 기존 리스트 비우기

        correctSubmissions.forEach(sub => {
            let submissionElement = document.createElement("div");
            submissionElement.innerHTML = `
                <p><strong>${sub.team_name || sub.user_name}</strong>: ${sub.challenge} - <span style="color: green">Correct</span></p>
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

---

### TimeStamp
<pre><code>
function formatTimestamp(isoString) {
    let date = new Date(isoString);
    let year = date.getFullYear();
    let month = String(date.getMonth() + 1).padStart(2, "0");
    let day = String(date.getDate()).padStart(2, "0");
    let hours = String(date.getHours()).padStart(2, "0");
    let minutes = String(date.getMinutes()).padStart(2, "0");
    let seconds = String(date.getSeconds()).padStart(2, "0");

    return `${year}.${month}.${day} ${hours}:${minutes}:${seconds}`;
}

function updateTS() {
    CTFd.api.get_submissions_list({ page: 1, per_page: 20 }).then(response => {
        if (!response.success) return;

        var ts = response.data;

        // correct: true인 데이터만 필터링하고 최신순으로 정렬
        var correctSubmissions = ts.filter(sub => sub.correct).slice(0, 10);

        var tsContainer = document.getElementById("ts-list");

        // 기존 리스트 비우기
        tsContainer.innerHTML = "";

        // 새로 가져온 데이터 추가
        correctSubmissions.forEach(sub => {
            let submissionElement = document.createElement("div");
            submissionElement.innerHTML = `<p>${formatTimestamp(sub.date)}</p>`;
            tsContainer.appendChild(submissionElement);
        });
    });
}

// 페이지 로드 시 실행
updateTS();

// 1분마다 업데이트
setInterval(updateTS, 60000);
</code></pre>

---

### Challenge Category
<pre><code>