### Profile - 1

<pre><code>
{% extends "base.html" %}

{% block stylesheets %}
    <link rel="stylesheet" href="{{ url_for('views.themes', path='css/custom-profile.css') }}">
{% endblock %}

{% block content %}
<div class="profile-container">
    <div class="profile-header">
        <div class="profile-info">
            <h1>{{ user.team.name if user.team_id else user.name }}</h1>
            <h2 class="points">{{ account.score }} pts</h2>
            <p class="last-update">Last update: 
                {% if solves %}
                    {{ solves[-1].date | datetimeformat }}
                {% else %}
                    N/A
                {% endif %}
            </p>
        </div>
        <div class="profile-actions">
            <h3>Members</h3>
            {% if user.team_id %}
                <p>{{ user.team.members | join(", ") }}</p>
            {% else %}
                <p>Solo player</p>
            {% endif %}
            <button class="invite-button">Copy invitation link</button>
        </div>
    </div>

    <div class="profile-solves">
        <table>
            <thead>
                <tr>
                    <th>Challenge</th>
                    <th>Category</th>
                    <th>Score</th>
                    <th>Solver</th>
                    <th>Solve at</th>
                </tr>
            </thead>
            <tbody>
                {% for solve in solves %}
                <tr>
                    <td>{{ solve.challenge.name }}</td>
                    <td>{{ solve.challenge.category }}</td>
                    <td>{{ solve.challenge.value }}</td>
                    <td>{{ user.name }}</td>
                    <td>{{ solve.date | datetimeformat }}</td>
                </tr>
                {% endfor %}
            </tbody>
        </table>
    </div>
</div>
{% endblock %}
</code></pre>

<pre><code>
.profile-container {
    max-width: 900px;
    margin: auto;
    color: #fff;
    background: #181818;
    padding: 20px;
    border-radius: 10px;
}

.profile-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 20px;
    border-bottom: 2px solid #444;
}

.profile-info h1 {
    font-size: 24px;
    font-weight: bold;
}

.profile-info .points {
    font-size: 22px;
    color: #ddd;
}

.profile-info .last-update {
    font-size: 14px;
    color: #aaa;
}

.profile-actions {
    text-align: right;
}

.invite-button {
    background: #4CAF50;
    color: white;
    border: none;
    padding: 8px 12px;
    cursor: pointer;
    border-radius: 5px;
    font-size: 14px;
}

.profile-solves {
    margin-top: 20px;
}

.profile-solves table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 10px;
}

.profile-solves th, .profile-solves td {
    border: 1px solid #444;
    padding: 8px;
    text-align: left;
}

.profile-solves th {
    background: #222;
}

.profile-solves tr:nth-child(even) {
    background: #282828;
}
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

### Scoreboard Graph Color
<pre><code>
const option = {
    title: {
        left: "center",
        text: "Top 10 " + (CTFd.config.userMode === "teams" ? "Teams" : "Users")
    },
    tooltip: {
        trigger: "axis",
        axisPointer: { type: "cross" }
    },
    legend: {
        type: "scroll",
        orient: "horizontal",
        align: "left",
        bottom: 35,
        data: []
    },
    toolbox: {
        feature: {
            dataZoom: { yAxisIndex: "none" },
            saveAsImage: {}
        }
    },
    grid: { containLabel: true },
    xAxis: [{ type: "time", boundaryGap: false, data: [] }],
    yAxis: [{ type: "value" }],
    dataZoom: [
        {
            id: "dataZoomX",
            type: "slider",
            xAxisIndex: [0],
            filterMode: "filter",
            height: 20,
            top: 35,
            fillerColor: "rgba(233, 236, 241, 0.4)"
        }
    ],
    series: []
};

// 색상 계산 함수 (흰색 → 회색)
function getRankColor(rank, totalRanks) {
    let lightness = 100 - (rank / totalRanks) * 60; // 100(흰색) → 40(회색)
    return `hsl(0, 0%, ${lightness}%)`;
}

for (let i = 0; i < teams.length; i++) {
    const team_score = [];
    const times = [];
    
    for (let j = 0; j < places[teams[i]]["solves"].length; j++) {
        team_score.push(places[teams[i]]["solves"][j].value);
        const date = dayjs(places[teams[i]]["solves"][j].date);
        times.push(date.toDate());
    }

    const total_scores = cumulativeSum(team_score);
    var scores = times.map((e, i) => [e, total_scores[i]]);

    option.legend.data.push(places[teams[i]]["name"]);

    const rank = i + 1;  // 순위 (1등부터 시작)
    const totalRanks = teams.length;

    const data = {
        name: places[teams[i]]["name"],
        type: "line",
        label: { normal: { position: "top" } },
        itemStyle: {
            normal: { color: getRankColor(rank, totalRanks) } // 순위 기반 색상 적용
        },
        data: scores
    };
    
    option.series.push(data);
}

return option;
</code></pre>

---

### Challenge Page (Note)
<pre><code>
/* 챌린지 페이지 전체 배경 */
body {
    background-color: #111;
    color: #ddd;
}

/* 챌린지 박스 스타일 */
.challenge-card {
    background-color: #222;
    border-radius: 10px;
    padding: 15px;
    text-align: center;
    box-shadow: 0 0 10px rgba(255, 255, 255, 0.1);
    transition: 0.3s;
}

.challenge-card:hover {
    box-shadow: 0 0 15px rgba(255, 255, 255, 0.2);
}

/* 챌린지 제목 */
.challenge-card .challenge-header {
    font-size: 16px;
    font-weight: bold;
    color: #0f0;
}

/* 챌린지 태그 */
.challenge-card .category {
    background-color: rgba(255, 255, 255, 0.1);
    padding: 3px 7px;
    border-radius: 5px;
    font-size: 12px;
}

/* 점수 표시 */
.challenge-card .points {
    font-size: 20px;
    font-weight: bold;
    color: #fff;
}

/* 문제를 풀었을 때 체크 아이콘 */
.solved-icon {
    color: #0f0;
    font-size: 16px;
}
</code></pre>

'''
<div class="container">
    <div class="row">
        {% for challenge in challenges %}
        <div class="col-md-3">
            <div class="challenge-card">
                <div class="challenge-header">
                    {{ challenge.name }}
                </div>
                <div class="challenge-meta">
                    <span class="category">{{ challenge.category }}</span>
                    <span class="points">{{ challenge.value }}</span>
                </div>
                {% if challenge.solved %}
                <div class="solved-icon">✔</div>
                {% endif %}
            </div>
        </div>
        {% endfor %}
    </div>
</div>
'''

---
