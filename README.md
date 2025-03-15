### Jeopardy & AD nav setup

<pre><code>
<div class="nav-tabs">
    <button onclick="showTab('jeopardy')">Jeopardy</button>
    <button onclick="showTab('attack-defense')">Attack & Defense</button>
</div>

<div id="jeopardy">
    {% include "challenges.html" %}
</div>

<div id="attack-defense" style="display:none;">
    {% include "attack_defense.html" %}
</div>

<script>
function showTab(tab) {
    document.getElementById('jeopardy').style.display = tab === 'jeopardy' ? 'block' : 'none';
    document.getElementById('attack-defense').style.display = tab === 'attack-defense' ? 'block' : 'none';
}
</script>
</code></pre>

---

### Attack_Defense.html
<code><pre>
{% extends "base.html" %}

{% block content %}
<h2>🏴 Attack & Defense Dashboard</h2>

<!-- 1. 팀별 서비스 상태 -->
<h3>🛠️ Service Status</h3>
<table>
    <thead>
        <tr>
            <th>Team</th>
            <th>Service 1</th>
            <th>Service 2</th>
            <th>Service 3</th>
        </tr>
    </thead>
    <tbody id="service-status">
        <!-- 여기에 팀별 서비스 상태가 AJAX로 업데이트됨 -->
    </tbody>
</table>

<!-- 2. 공격 기록 -->
<h3>⚔️ Attack Logs</h3>
<table>
    <thead>
        <tr>
            <th>Timestamp</th>
            <th>Attacker</th>
            <th>Victim</th>
            <th>Flag</th>
        </tr>
    </thead>
    <tbody id="attack-logs">
        <!-- 공격 로그가 실시간으로 추가됨 -->
    </tbody>
</table>

<!-- 3. 점수판 -->
<h3>🏆 Team Scores</h3>
<table>
    <thead>
        <tr>
            <th>Team</th>
            <th>Jeopardy Score</th>
            <th>Attack Score</th>
            <th>Defense Score</th>
            <th>Total Score</th>
        </tr>
    </thead>
    <tbody id="scoreboard">
        <!-- 점수판이 실시간 업데이트됨 -->
    </tbody>
</table>

<script>
    function updateServiceStatus() {
        fetch('/api/attack_defense/status')
            .then(response => response.json())
            .then(data => {
                let statusTable = document.getElementById('service-status');
                statusTable.innerHTML = '';
                data.services.forEach(team => {
                    let row = `<tr>
                        <td>${team.name}</td>
                        <td>${team.service1 ? "✅ Running" : "❌ Down"}</td>
                        <td>${team.service2 ? "✅ Running" : "❌ Down"}</td>
                        <td>${team.service3 ? "✅ Running" : "❌ Down"}</td>
                    </tr>`;
                    statusTable.innerHTML += row;
                });
            });
    }

    function updateAttackLogs() {
        fetch('/api/attack_defense/logs')
            .then(response => response.json())
            .then(data => {
                let logsTable = document.getElementById('attack-logs');
                logsTable.innerHTML = '';
                data.logs.forEach(log => {
                    let row = `<tr>
                        <td>${log.timestamp}</td>
                        <td>${log.attacker}</td>
                        <td>${log.victim}</td>
                        <td>${log.flag}</td>
                    </tr>`;
                    logsTable.innerHTML += row;
                });
            });
    }

    function updateScoreboard() {
        fetch('/api/attack_defense/scores')
            .then(response => response.json())
            .then(data => {
                let scoreboardTable = document.getElementById('scoreboard');
                scoreboardTable.innerHTML = '';
                data.scores.forEach(team => {
                    let row = `<tr>
                        <td>${team.name}</td>
                        <td>${team.jeopardy_score}</td>
                        <td>${team.attack_score}</td>
                        <td>${team.defense_score}</td>
                        <td>${team.total_score}</td>
                    </tr>`;
                    scoreboardTable.innerHTML += row;
                });
            });
    }

    setInterval(updateServiceStatus, 5000);
    setInterval(updateAttackLogs, 5000);
    setInterval(updateScoreboard, 5000);
</script>

{% endblock %}
</code></pre>

---

### 공격 로그 조회

<pre><code>
@app.route('/api/attack_defense/logs')
def get_attack_logs():
    logs = db.session.execute("SELECT timestamp, attacker_team_id, victim_team_id, flag FROM attack_logs").fetchall()
    formatted_logs = [
        {
            "timestamp": log.timestamp.strftime("%Y-%m-%d %H:%M:%S"),
            "attacker": f"Team {log.attacker_team_id}",
            "victim": f"Team {log.victim_team_id}",
            "flag": log.flag
        } for log in logs
    ]
    return jsonify({"logs": formatted_logs})
</code></pre>