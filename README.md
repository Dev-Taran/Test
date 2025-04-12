### challenges.html
{% extends "base.html" %}

{% block content %}
<div class="challenge-container">
    <!-- 왼쪽 필터 섹션 -->
    <aside class="challenge-sidebar">
        <h2>카테고리</h2>
        <ul>
            <li><input type="checkbox" checked> All ({{ challenges|length }})</li>
            {% for category in categories %}
                <li><input type="checkbox"> {{ category }} ({{ categories[category] }})</li>
            {% endfor %}
        </ul>
    </aside>

    <!-- 중앙 문제 리스트 -->
    <main class="challenge-list">
        {% for challenge in challenges %}
        <div class="challenge-card">
            <div class="challenge-header">
                <h3>{{ challenge.name }}</h3>
                <span class="points">{{ challenge.value }} pts</span>
            </div>
            <p>{{ challenge.category }}</p>
            <div class="challenge-footer">
                <span class="solves">{{ challenge.solves }} solved</span>
                <button onclick="window.location.href='/challenges/{{ challenge.id }}'">도전하기</button>
            </div>
        </div>
        {% endfor %}
    </main>

    <!-- 오른쪽 유저 프로필 -->
    <aside class="user-profile">
        <h2>{{ user.name }}</h2>
        <p>순위: #{{ user.rank }}</p>
        <p>점수: {{ user.score }}</p>
        <p>푼 문제: {{ user.solves }}</p>
    </aside>
</div>
{% endblock %}


### custom.css
.challenge-container {
    display: flex;
    gap: 20px;
    padding: 20px;
}

/* 왼쪽 필터 스타일 */
.challenge-sidebar {
    width: 200px;
    background: #222;
    color: white;
    padding: 15px;
    border-radius: 8px;
}

/* 중앙 문제 리스트 스타일 */
.challenge-list {
    flex-grow: 1;
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 15px;
}

/* 문제 카드 스타일 */
.challenge-card {
    background: #333;
    padding: 15px;
    border-radius: 8px;
    color: white;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
}

.challenge-header {
    display: flex;
    justify-content: space-between;
    font-weight: bold;
}

.challenge-footer {
    margin-top: 10px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.challenge-card button {
    background: #00bcd4;
    border: none;
    padding: 5px 10px;
    color: white;
    cursor: pointer;
    border-radius: 4px;
}

/* 오른쪽 유저 프로필 */
.user-profile {
    width: 200px;
    background: #222;
    color: white;
    padding: 15px;
    border-radius: 8px;
}

