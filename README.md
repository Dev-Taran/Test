# Test

scoreboard #1

function s() {
    return n.default.api.get_scoreboard_detail({ count: 10 }).then(function (e) {
        var s = e.data,
            d = Object.keys(s);
        if (0 === d.length) return !1;

        let yAxisData = [];
        let colorGradient = [];

        for (let i = 0; i < d.length; i++) {
            yAxisData.push(s[d[i]].name);
            
            // 1등은 완전 흰색, 뒤로 갈수록 회색
            let grayLevel = Math.floor(255 - (i * 15));
            if (grayLevel < 170) grayLevel = 170;
            colorGradient.push(`rgb(${grayLevel}, ${grayLevel}, ${grayLevel})`);
        }

        let p = {
            title: {
                left: "center",
                text: "Top 10 " + ("teams" === n.default.config.userMode ? "Teams" : "Users"),
                textStyle: { color: "white" }
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
                data: yAxisData
            },
            toolbox: {
                feature: {
                    dataZoom: { yAxisIndex: "none" },
                    saveAsImage: {}
                }
            },
            grid: { containLabel: true },
            xAxis: [{
                type: "time",
                boundaryGap: false,
                axisLabel: { color: "white" }
            }],
            yAxis: [{
                type: "category",
                data: yAxisData,  // y축에 팀 이름 설정
                axisLabel: { color: "white" },
                splitLine: { show: false }
            }],
            dataZoom: [{
                id: "dataZoomX",
                type: "slider",
                xAxisIndex: [0],
                filterMode: "filter",
                height: 20,
                top: 35,
                fillerColor: "rgba(233, 236, 241, 0.4)"
            }],
            series: []
        };

        console.log("Y축 데이터:", yAxisData);  // 디버깅 로그

        for (let t = 0; t < d.length; t++) {
            let teamScores = [];
            let timePoints = [];

            for (let o = 0; o < s[d[t]].solves.length; o++) {
                teamScores.push(s[d[t]].solves[o].value);
                let date = (0, l.default)(s[d[t]].solves[o].date);
                timePoints.push(date.toDate());
            }

            let totalScores = (0, u.cumulativeSum)(teamScores);
            let scoreData = timePoints.map((e, i) => [e, totalScores[i]]);

            console.log(`팀 ${s[d[t]].name} 데이터:`, scoreData);  // 디버깅 로그

            if (scoreData.length === 0) continue;  // 데이터가 없으면 스킵

            let teamGraph = {
                name: s[d[t]].name,
                type: "line",
                smooth: true,  // 선 부드럽게
                showSymbol: false,  // 마커 제거
                label: { normal: { position: "right" } },
                itemStyle: { normal: { color: colorGradient[t] } },
                data: scoreData
            };

            p.series.push(teamGraph);
        }

        return p;
    });
}


scoreboard #2
fetch("/api/v1/scoreboard/graph")
    .then(response => response.json())
    .then(data => {
        var chart = echarts.init(document.getElementById('score-graph'));

        var teams = data.data;
        var series = [];
        
        teams.forEach((team, index) => {
            let colorIntensity = 255 - (index * 20);  // 1등은 흰색(255), 뒤로 갈수록 회색(감소)
            if (colorIntensity < 100) colorIntensity = 100;  // 너무 어두워지지 않도록 최소값 설정

            let teamColor = `rgb(${colorIntensity}, ${colorIntensity}, ${colorIntensity})`;

            series.push({
                name: team.name,
                type: 'line',
                data: team.series,
                lineStyle: { color: teamColor },
                smooth: true
            });
        });

        var option = {
            backgroundColor: 'black',
            textStyle: { color: 'white' },
            xAxis: {
                type: 'time',
                boundaryGap: false,
                axisLabel: { color: 'white' },
                splitLine: { show: false }
            },
            yAxis: {
                type: 'value',
                axisLabel: { color: 'white' },
                splitLine: { show: false }
            },
            series: series
        };

        chart.setOption(option);
    });


submission #1
function updateSubmissions() {
    CTFd.api.get_submissions_list({ page: 1, per_page: 10 }).then(response => {
        if (!response.success) return;
        
        var submissions = response.data;
        var submissionsContainer = document.getElementById("submissions-list");

        // 기존 리스트 비우기
        submissionsContainer.innerHTML = "";

        // 새로 가져온 데이터 추가
        submissions.forEach(sub => {
            let submissionElement = document.createElement("div");
            submissionElement.innerHTML = `
                <p><strong>${sub.team_name || sub.user_name}</strong>: ${sub.challenge} - <span style="color: ${sub.correct ? 'green' : 'red'}">
                ${sub.correct ? "Correct" : "Incorrect"}</span></p>
            `;
            submissionsContainer.appendChild(submissionElement);
        });
    });
}

// 페이지 로드 시 실행
updateSubmissions();

// 1분마다 업데이트
setInterval(updateSubmissions, 60000);
