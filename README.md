### Jeopardy & AD nav setup

<code>
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
</code>