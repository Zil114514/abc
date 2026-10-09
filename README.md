<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no" />
<title>3D 钓鱼游戏 </title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }
  html, body { width: 100%; height: 100%; overflow: hidden; background: #0b1015; touch-action: none; }
  canvas { display: block; }
  #hint {
    position: fixed; inset: 0; display: flex; flex-direction: column;
    align-items: center; justify-content: center; gap: 14px;
    background: radial-gradient(circle at 50% 45%, rgba(10,25,40,.55), rgba(4,10,18,.88));
    color: #eaf3fb; font-family: "Microsoft YaHei", "PingFang SC", system-ui, sans-serif;
    text-align: center; user-select: none; transition: opacity .3s ease; z-index: 10;
  }
  #hint.hidden { opacity: 0; pointer-events: none; }
  #hint h1 { font-size: 30px; letter-spacing: 6px; font-weight: 600; }
  #hint p { font-size: 15px; opacity: .8; line-height: 2; }
  #hint b { color: #7fd4ff; font-weight: 600; }

  #joystick-base {
    position: fixed; bottom: 40px; left: 40px;
    width: 180px; height: 180px; border-radius: 50%;
    background: rgba(255,255,255,0.06);
    border: 2px solid rgba(255,255,255,0.18);
    z-index: 20; touch-action: none;
  }
  #joystick-knob {
    position: absolute; top: 50%; left: 50%;
    width: 70px; height: 70px; margin: -35px 0 0 -35px;
    border-radius: 50%; background: rgba(255,255,255,0.35);
    box-shadow: 0 0 14px rgba(0,0,0,0.35);
    z-index: 21; pointer-events: none;
  }
  #tip {
    position: fixed; bottom: 50px; right: 34px;
    color: rgba(255,255,255,0.4);
    font-family: "Microsoft YaHei", sans-serif;
    font-size: 12px; text-align: right; line-height: 1.9;
    z-index: 20; pointer-events: none;
  }
  #inventory {
    position: fixed; bottom: 34px; right: 20px;
    display: flex; gap: 6px; z-index: 25; pointer-events: none;
  }
  .slot {
    width: 62px; height: 62px; border-radius: 6px;
    background: rgba(25, 28, 32, 0.78);
    border: 1px solid rgba(200, 200, 200, 0.28);
    position: relative; display: flex;
    align-items: center; justify-content: center;
    color: #e8e8e8;
    font-family: "STSong", "SimSun", "Microsoft YaHei", serif;
    overflow: hidden; cursor: pointer; user-select: none;
    pointer-events: auto;
    transition: border-color 0.15s, transform 0.12s;
    touch-action: none;
  }
  .slot.held { border-color: #d8c88a; transform: translateY(-3px); }
  .slot.has-bait { border-color: #a8b878; }
  .slot .icon { font-size: 26px; line-height: 1; color: #e0d6c0; font-family: "STSong", "SimSun", serif; font-weight: 500; }
  .slot .item-count { position: absolute; top: 1px; right: 5px; font-size: 11px; color: #ccc; font-family: "Microsoft YaHei", sans-serif; }
  .slot .bait-mark { position: absolute; top: 2px; left: 5px; font-size: 9px; color: #b8d888; font-family: "Microsoft YaHei", sans-serif; }
  .slot .durability { position: absolute; bottom: 0; left: 0; height: 3px; background: #b8a878; transition: width 0.2s; }
  .slot .item-name { position: absolute; bottom: 4px; width: 100%; text-align: center; font-size: 9px; color: rgba(230, 225, 215, 0.75); font-family: "Microsoft YaHei", sans-serif; white-space: nowrap; overflow: hidden; }

  #player-stamina {
    position: fixed; bottom: 108px; right: 20px;
    width: 334px; height: 8px;
    background: rgba(25, 28, 32, 0.7); border-radius: 2px;
    overflow: hidden; border: 1px solid rgba(200, 200, 200, 0.2);
    z-index: 25; pointer-events: none;
  }
  #player-stamina .fill { height: 100%; background: #c8b888; width: 100%; transition: width 0.15s; }
  #player-stamina .label { position: absolute; top: -15px; left: 0; font-size: 10px; color: rgba(200, 200, 200, 0.6); font-family: "Microsoft YaHei", sans-serif; letter-spacing: 2px; }

  #pickup-btn {
    position: fixed; bottom: 108px; left: 50%; transform: translateX(-50%);
    display: none; flex-direction: column; align-items: center; gap: 5px;
    z-index: 30; cursor: pointer; user-select: none; touch-action: none;
    opacity: 0; transition: opacity 0.22s, transform 0.18s;
  }
  #pickup-btn.show { display: flex; opacity: 1; }
  #pickup-btn:active { transform: translateX(-50%) scale(0.94); }
  #pickup-btn .ring {
    width: 62px; height: 62px; border-radius: 50%;
    border: 1.5px solid rgba(240, 240, 240, 0.8);
    background: rgba(30, 32, 36, 0.4);
    display: flex; align-items: center; justify-content: center;
  }
  #pickup-btn .ring-icon { font-size: 22px; line-height: 1; color: #e8e0c8; font-family: "STSong", "SimSun", serif; font-weight: 500; }
  #pickup-btn .pickup-label { color: #e8e0c8; font-family: "Microsoft YaHei", sans-serif; font-size: 11px; letter-spacing: 3px; text-shadow: 0 2px 6px rgba(0, 0, 0, 0.9); padding-left: 3px; opacity: 0.8; }

  #cast-btn, #reel-btn, #net-btn, #basket-btn {
    position: fixed; right: 30px;
    width: 86px; height: 86px; border-radius: 50%;
    padding: 0; font-family: "STSong", "SimSun", "Microsoft YaHei", serif;
    font-size: 14px; font-weight: 500; letter-spacing: 2px;
    color: #e8e0c8; background: rgba(40, 38, 34, 0.82);
    border: 1.5px solid rgba(200, 190, 160, 0.55);
    cursor: pointer; user-select: none; z-index: 32;
    text-shadow: 0 1px 3px rgba(0,0,0,0.7);
    transition: opacity .2s, background .12s;
    touch-action: none; display: none;
    text-align: center; line-height: 86px;
  }
  #cast-btn { bottom: 460px; }
  #basket-btn { bottom: 360px; }
  #net-btn { bottom: 260px; }
  #reel-btn { bottom: 160px; }
  #cast-btn.show, #reel-btn.show, #net-btn.show, #basket-btn.show { display: block; }
  #cast-btn:active, #reel-btn:active, #net-btn:active, #basket-btn:active { background: rgba(80, 74, 60, 0.95); }

  #shop-btn {
    position: fixed; bottom: 230px; left: 50%; transform: translateX(-50%);
    padding: 14px 34px; background: rgba(40, 38, 34, 0.85);
    border: 1.5px solid rgba(200, 190, 160, 0.55);
    border-radius: 4px; color: #e8e0c8;
    font-family: "STSong", "SimSun", "Microsoft YaHei", serif;
    font-size: 16px; letter-spacing: 4px;
    cursor: pointer; z-index: 33;
    display: none; user-select: none; touch-action: none;
    box-shadow: 0 4px 14px rgba(0,0,0,0.5);
  }
  #shop-btn.show { display: block; }
  #shop-btn:active { background: rgba(80, 74, 60, 0.95); }

  #setting-btn{
    position:fixed;top:14px;right:14px;z-index:45;
    width:44px;height:44px;border-radius:6px;
    background:rgba(40,38,34,0.75);border:1px solid #b8a878;
    color:#e8e0c8;font-size:20px;cursor:pointer;
    display:flex;align-items:center;justify-content:center;
  }

  .modal-mask{
    position:fixed;inset:0;background:rgba(0,0,0,0.65);z-index:98;display:none;
  }
  .modal-mask.show{display:block;}

  #shop-panel {
    position: fixed; top: 50%; left: 50%;
    transform: translate(-50%, -50%) scale(0.95);
    width: 92%; max-width: 440px; max-height: 78vh;
    background: rgba(28, 24, 20, 0.97);
    border: 2px solid rgba(180, 160, 110, 0.5);
    border-radius: 6px; z-index: 100;
    display: none; flex-direction: column;
    font-family: "STSong", "SimSun", "Microsoft YaHei", serif;
    color: #e8e0c8; box-shadow: 0 14px 50px rgba(0, 0, 0, 0.85);
    opacity: 0; transition: opacity 0.25s, transform 0.25s;
  }
  #shop-panel.show { display: flex; opacity: 1; transform: translate(-50%, -50%) scale(1); }
  #shop-panel .shop-header { display: flex; justify-content: space-between; align-items: center; padding: 14px 18px; border-bottom: 1px solid rgba(180, 160, 110, 0.3); }
  #shop-panel .shop-title { font-size: 17px; letter-spacing: 4px; }
  #shop-panel .shop-close { font-size: 24px; cursor: pointer; color: #b8a878; padding: 0 8px; line-height: 1; touch-action: none; }
  #shop-panel .shop-balance { padding: 10px 18px; font-size: 13px; color: #c8b888; border-bottom: 1px solid rgba(180, 160, 110, 0.2); letter-spacing: 2px; }
  #shop-panel .shop-balance span { color: #e8c878; font-weight: 600; }
  #shop-panel .shop-tabs { display: flex; border-bottom: 1px solid rgba(180, 160, 110, 0.25); }
  #shop-panel .shop-tab { flex: 1; padding: 10px 0; text-align: center; font-size: 13px; letter-spacing: 3px; color: #a89878; cursor: pointer; border-bottom: 2px solid transparent; transition: all 0.15s; user-select: none; touch-action: none; }
  #shop-panel .shop-tab.active { color: #e8c878; border-bottom-color: #e8c878; }
  #shop-panel .shop-list { flex: 1; overflow-y: auto; padding: 8px; -webkit-overflow-scrolling: touch; }
  #shop-panel .shop-item { display: flex; align-items: center; padding: 10px 12px; margin: 4px 0; background: rgba(45, 40, 32, 0.7); border-radius: 4px; font-size: 13px; }
  #shop-panel .shop-item .fish-icon { width: 36px; height: 36px; border-radius: 4px; border: 2px solid #e0e0e0; display: flex; align-items: center; justify-content: center; margin-right: 12px; font-size: 15px; color: #e0e0e0; font-family: "STSong", "SimSun", serif; flex-shrink: 0; }
  #shop-panel .shop-item .fish-info { flex: 1; min-width: 0; }
  #shop-panel .shop-item .fish-name { display: block; margin-bottom: 3px; font-size: 14px; letter-spacing: 1px; }
  #shop-panel .shop-item .fish-meta { font-size: 11px; color: #a89878; letter-spacing: 1px; }
  #shop-panel .shop-item .sell-btn, #shop-panel .shop-item .buy-btn { padding: 7px 14px; background: rgba(65, 58, 44, 0.9); border: 1px solid rgba(180, 160, 110, 0.55); border-radius: 3px; color: #e8e0c8; font-family: inherit; font-size: 12px; letter-spacing: 2px; cursor: pointer; touch-action: none; flex-shrink: 0; }
  #shop-panel .shop-item .sell-btn:active, #shop-panel .shop-item .buy-btn:active { background: rgba(90, 80, 60, 0.95); }
  #shop-panel .shop-item .buy-btn.disabled { opacity: 0.4; pointer-events: none; }
  #shop-panel .empty-msg { text-align: center; padding: 50px 20px; color: #887860; font-size: 14px; letter-spacing: 2px; }

  #setting-panel{
    position:fixed;top:50%;left:50%;transform:translate(-50%,-50%) scale(0.95);
    width:88%;max-width:400px;z-index:100;
    background:rgba(28,24,20,0.97);border:2px solid #b8a878;border-radius:6px;
    padding:18px;color:#e8e0c8;display:none;opacity:0;transition:0.25s;
  }
  #setting-panel.show{display:block;opacity:1;transform:translate(-50%,-50%) scale(1);}
  .setting-row{margin:12px 0;}
  .setting-row label{display:block;margin-bottom:4px;font-size:14px;}
  .setting-btn{width:100%;padding:10px;margin-top:8px;background:rgba(60,50,40,0.85);border:1px solid #b8a878;color:#e8e0c8;border-radius:4px;font-size:14px;}

  #fish-info {
    position: fixed; top: 90px; left: 50%; transform: translateX(-50%);
    display: none; flex-direction: column; align-items: center; gap: 8px;
    z-index: 30; pointer-events: none;
    font-family: "Microsoft YaHei", sans-serif;
  }
  #fish-info.show { display: flex; }
  #fish-info .title { color: #e8e0c8; font-size: 15px; letter-spacing: 6px; text-shadow: 0 2px 6px rgba(0,0,0,0.9); background: rgba(25, 28, 32, 0.7); padding: 5px 20px; border-radius: 2px; border: 1px solid rgba(200, 190, 160, 0.4); font-family: "STSong", "SimSun", serif; }
  #fish-info .fish-stamina { width: 200px; height: 8px; background: rgba(25, 28, 32, 0.75); border-radius: 1px; overflow: hidden; border: 1px solid rgba(200, 190, 160, 0.35); }
  #fish-info .fish-stamina .fill { height: 100%; width: 100%; background: #c8b888; transition: width 0.1s; }
  #fish-info .fish-state { color: #e8d8a8; font-size: 12px; text-shadow: 0 2px 6px rgba(0,0,0,0.9); letter-spacing: 3px; }

  #save-indicator {
    position: fixed; top: 12px; left: 12px;
    padding: 5px 10px; background: rgba(40, 60, 40, 0.6);
    border-radius: 3px; color: #a8d8a8;
    font-family: "Microsoft YaHei", sans-serif; font-size: 10px; letter-spacing: 1px;
    z-index: 40; pointer-events: none;
    opacity: 0; transition: opacity 0.4s;
  }
  #save-indicator.show { opacity: 1; }

  #toast {
    position: fixed; top: 46%; left: 50%;
    transform: translate(-50%, -50%) scale(0.95);
    padding: 10px 22px; background: rgba(20, 20, 20, 0.85);
    border: 1px solid rgba(200, 190, 160, 0.35);
    border-radius: 4px; color: #e8e0c8;
    font-family: "Microsoft YaHei", sans-serif; font-size: 13px; letter-spacing: 2px;
    z-index: 60; pointer-events: none;
    opacity: 0; transition: opacity 0.2s, transform 0.2s; white-space: nowrap;
  }
  #toast.show { opacity: 1; transform: translate(-50%, -50%) scale(1); }

  #confirm-dialog{
    position:fixed;top:50%;left:50%;transform:translate(-50%,-50%);
    background:#1c1814;border:2px solid #b8a878;border-radius:6px;
    z-index:120;padding:20px;color:#e8e0c8;display:none;max-width:320px;
  }
  #confirm-dialog.show{display:block;}
  .confirm-buttons{display:flex;gap:10px;margin-top:14px;}
  .confirm-buttons button{flex:1;padding:8px;background:rgba(60,50,40,0.8);border:1px solid #b8a878;color:#e8e0c8;border-radius:4px;}

  #err { position: fixed; inset: 0; display: none; align-items: center; justify-content: center; padding: 40px; background: #101720; color: #ffd9d9; font-family: "Microsoft YaHei", system-ui, sans-serif; font-size: 16px; line-height: 2; text-align: center; z-index: 50; }
  #err.show { display: flex; }
</style>
</head>
<body>
<div id="hint">
  <h1>钓 鱼 湖</h1>
  <p>
    点击画面进入<br />
    <b>左摇杆</b> 移动 &nbsp;·&nbsp; <b>右侧滑动</b> 转视角<br />
    上下滑动可抬头看天空
  </p>
</div>

<div id="setting-btn">⚙</div>
<div class="modal-mask" id="global-mask"></div>

<div id="setting-panel">
  <h3>设置</h3>
  <div class="setting-row">
    <label><input type="checkbox" id="audio-toggle" checked> 开启音效</label>
  </div>
  <div class="setting-row">
    <label>昼夜速度：<span id="day-speed-val">1x</span></label>
    <input type="range" min="0.25" max="4" step="0.25" value="1" id="day-speed-slider" style="width:100%">
  </div>
  <div class="setting-row">
    <button class="setting-btn" id="skip-day-btn">快进昼夜</button>
  </div>
  <div class="setting-row">
    <button class="setting-btn" id="reset-save-btn">重置存档（不可恢复）</button>
  </div>
  <div class="setting-row">
    <button class="setting-btn" id="close-setting-btn">关闭</button>
  </div>
</div>

<div id="confirm-dialog">
  <div id="confirm-text"></div>
  <div class="confirm-buttons">
    <button id="confirm-no">取消</button>
    <button id="confirm-yes">确定</button>
  </div>
</div>

<div id="joystick-base"><div id="joystick-knob"></div></div>
<div id="tip">左侧摇杆移动<br />右侧滑动转视角</div>
<div id="inventory">
  <div class="slot" data-i="0"></div>
  <div class="slot" data-i="1"></div>
  <div class="slot" data-i="2"></div>
  <div class="slot" data-i="3"></div>
  <div class="slot" data-i="4"></div>
</div>
<div id="player-stamina">
  <div class="label">体力</div>
  <div class="fill" id="player-stamina-fill"></div>
</div>
<div id="pickup-btn">
  <div class="ring"><div class="ring-icon">拾</div></div>
  <div class="pickup-label">拾取</div>
</div>
<div id="cast-btn">抛 竿</div>
<div id="basket-btn">放 筐</div>
<div id="net-btn">布 网</div>
<div id="reel-btn">收 线</div>
<div id="shop-btn">与店主交谈</div>
<div id="shop-panel">
  <div class="shop-header">
    <div class="shop-title">钓具小铺</div>
    <div class="shop-close" id="shop-close">×</div>
  </div>
  <div class="shop-balance">银两：<span id="shop-money">0</span> 文</div>
  <div class="shop-tabs">
    <div class="shop-tab active" data-tab="sell">出 售</div>
    <div class="shop-tab" data-tab="buy">购 买</div>
  </div>
  <div class="shop-list" id="shop-list"></div>
</div>
<div id="fish-info">
  <div class="title" id="fish-title">鱼</div>
  <div class="fish-stamina"><div class="fill" id="fish-stamina-fill"></div></div>
  <div class="fish-state" id="fish-state">上钩了</div>
</div>
<div id="save-indicator">已保存</div>
<div id="toast"></div>
<div id="err">
  <div>
    <div style="font-size:22px;margin-bottom:12px;">⚠ Three.js 加载失败</div>
    请检查网络连接后刷新页面。
  </div>
</div>
<script src="https://cdn.jsdelivr.net/npm/three@0.148.0/build/three.min.js"></script>
<script>
  if (!window.THREE) {
    document.write('<scr' + 'ipt src="https://unpkg.com/three@0.148.0/build/three.min.js"><\/scr' + 'ipt>');
  }
</script>
<script>
  if (!window.THREE) {
    document.write('<scr' + 'ipt src="https://cdn.bootcdn.net/ajax/libs/three.js/0.148.0/three.min.js"><\/scr' + 'ipt>');
  }
</script>
<script>
(function () {
  'use strict';
  if (!window.THREE) {
    document.getElementById('err').classList.add('show');
    document.getElementById('hint').style.display = 'none';
    return;
  }

  /* ========== 新增设置全局变量 ========== */
  var SETTINGS = {
    audioEnabled: true,
    daySpeedMult: 1.0
  };
  var modalMask = document.getElementById('global-mask');
  var settingBtn = document.getElementById('setting-btn');
  var settingPanel = document.getElementById('setting-panel');
  var audioToggle = document.getElementById('audio-toggle');
  var daySpeedSlider = document.getElementById('day-speed-slider');
  var daySpeedVal = document.getElementById('day-speed-val');
  var skipDayBtn = document.getElementById('skip-day-btn');
  var resetSaveBtn = document.getElementById('reset-save-btn');
  var closeSettingBtn = document.getElementById('close-setting-btn');
  var confirmDialog = document.getElementById('confirm-dialog');
  var confirmText = document.getElementById('confirm-text');
  var confirmYes = document.getElementById('confirm-yes');
  var confirmNo = document.getElementById('confirm-no');
  function showConfirm(text, yesCb){
    confirmText.innerText = text;
    confirmDialog.classList.add('show');
    modalMask.classList.add('show');
    confirmYes.onclick = function(){
      confirmDialog.classList.remove('show'); modalMask.classList.remove('show');
      yesCb();
    };
    confirmNo.onclick = function(){
      confirmDialog.classList.remove('show'); modalMask.classList.remove('show');
    }
  }
  function openSetting(){
    settingPanel.classList.add('show'); modalMask.classList.add('show');
  }
  function closeSetting(){
    settingPanel.classList.remove('show'); modalMask.classList.remove('show');
  }
  settingBtn.onclick = openSetting;
  closeSettingBtn.onclick = closeSetting;
  modalMask.onclick = function(){
    closeSetting();
    document.getElementById('shop-panel').classList.remove('show');
  }
  audioToggle.onchange = function(){ SETTINGS.audioEnabled = audioToggle.checked; };
  daySpeedSlider.oninput = function(){
    SETTINGS.daySpeedMult = parseFloat(daySpeedSlider.value);
    daySpeedVal.innerText = SETTINGS.daySpeedMult + "x";
  }
  skipDayBtn.onclick = function(){ dayPhase += 0.25; if(dayPhase>1) dayPhase -=1; }
  resetSaveBtn.onclick = function(){
    showConfirm("确定要全部重置存档？所有进度将永久丢失！", function(){
      localStorage.removeItem('fishingGame_v6');
      location.reload();
    })
  }

  /* ===================== 音效 增加开关判断 ===================== */
  var AudioManager = (function () {
    var ctx = null; var started = false;
    function init() {
      if (!ctx) { try { ctx = new (window.AudioContext || window.webkitAudioContext)(); } catch (e) { return; } }
      if (ctx.state === 'suspended') ctx.resume();
    }
    function noiseBurst(duration, freq, q, gainVal, filterType) {
      if(!SETTINGS.audioEnabled) return;
      if (!ctx) return;
      var sr = ctx.sampleRate;
      var bufferSize = Math.max(1, Math.floor(sr * duration));
      var buffer = ctx.createBuffer(1, bufferSize, sr);
      var data = buffer.getChannelData(0);
      for (var i = 0; i < bufferSize; i++) data[i] = Math.random() * 2 - 1;
      var src = ctx.createBufferSource(); src.buffer = buffer;
      var filter = ctx.createBiquadFilter();
      filter.type = filterType || 'bandpass';
      filter.frequency.value = freq; filter.Q.value = q;
      var gain = ctx.createGain();
      var now = ctx.currentTime;
      gain.gain.setValueAtTime(gainVal, now);
      gain.gain.exponentialRampToValueAtTime(0.001, now + duration);
      src.connect(filter); filter.connect(gain); gain.connect(ctx.destination);
      src.start(now); src.stop(now + duration);
    }
    function tone(freq, duration, gainVal, type) {
      if(!SETTINGS.audioEnabled) return;
      if (!ctx) return;
      var osc = ctx.createOscillator();
      osc.type = type || 'sine'; osc.frequency.value = freq;
      var gain = ctx.createGain();
      var now = ctx.currentTime;
      gain.gain.setValueAtTime(gainVal, now);
      gain.gain.exponentialRampToValueAtTime(0.001, now + duration);
      osc.connect(gain); gain.connect(ctx.destination);
      osc.start(now); osc.stop(now + duration);
    }
    return {
      init: init,
      startAmbient: function () {
        if(!SETTINGS.audioEnabled) return;
        init(); if (!ctx || started) return; started = true;
        var sr = ctx.sampleRate;
        var bufferSize = sr * 4;
        var buffer = ctx.createBuffer(1, bufferSize, sr);
        var data = buffer.getChannelData(0);
        for (var i = 0; i < bufferSize; i++) data[i] = (Math.random() * 2 - 1) * (0.5 + Math.sin(i / sr * 0.5) * 0.3);
        var src = ctx.createBufferSource(); src.buffer = buffer; src.loop = true;
        var filter = ctx.createBiquadFilter();
        filter.type = 'lowpass'; filter.frequency.value = 320;
        var gain = ctx.createGain(); gain.gain.value = 0.025;
        src.connect(filter); filter.connect(gain); gain.connect(ctx.destination);
        src.start();
      },
      splash: function (p) { init(); noiseBurst(0.4, 500 + Math.max(0.4, Math.min(2, p || 1)) * 200, 1.2, 0.08, 'lowpass'); },
      cast: function () {
        if(!SETTINGS.audioEnabled) return;
        init(); if (!ctx) return;
        var osc = ctx.createOscillator(); osc.type = 'sine';
        var now = ctx.currentTime;
        osc.frequency.setValueAtTime(200, now);
        osc.frequency.exponentialRampToValueAtTime(900, now + 0.25);
        var gain = ctx.createGain();
        gain.gain.setValueAtTime(0.05, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 0.3);
        osc.connect(gain); gain.connect(ctx.destination);
        osc.start(now); osc.stop(now + 0.3);
      },
      hook: function () {
        if(!SETTINGS.audioEnabled) return;
        init();
        if(navigator.vibrate) navigator.vibrate(80);
        tone(660, 0.12, 0.06, 'sine'); setTimeout(function () { tone(990, 0.18, 0.05, 'sine'); }, 90);
      },
      reel: function () { init(); tone(1100, 0.03, 0.03, 'square'); },
      pickup: function () { init(); tone(660, 0.09, 0.055, 'sine'); setTimeout(function () { tone(880, 0.14, 0.05, 'sine'); }, 60); },
      coin: function () { init(); tone(1320, 0.07, 0.06, 'sine'); setTimeout(function () { tone(1760, 0.13, 0.055, 'sine'); }, 50); },
      birdCry: function () { init(); tone(900, 0.15, 0.04, 'sawtooth'); setTimeout(function () { tone(780, 0.2, 0.035, 'sawtooth'); }, 140); }
    };
  })();
  var CFG = {
    groundA: 70, groundB: 54,
    walkA: 52, walkB: 40,
    lakeX: 0, lakeZ: -14,
    lakeA: 34, lakeB: 16,
    rimA: 36, rimB: 18,
    waterY: -0.65,
    spawn: { x: 0, z: 16 },
    shop:  { x: 0, z: 28 }
  };
  var SUN_DIR = new THREE.Vector3(-75, 80, 50).normalize();
  var FOG_COLOR = new THREE.Color(0xc6dae8);
  function setRenderSRGB(r) {
    if ('outputColorSpace' in r && THREE.SRGBColorSpace) r.outputColorSpace = THREE.SRGBColorSpace;
    else if ('outputEncoding' in r) r.outputEncoding = THREE.sRGBEncoding;
  }
  function setTexSRGB(tex) {
    if ('colorSpace' in tex && THREE.SRGBColorSpace) tex.colorSpace = THREE.SRGBColorSpace;
    else if ('encoding' in tex) tex.encoding = THREE.sRGBEncoding;
  }
  var IS_MOBILE = window.innerWidth < 1024 || /Android|iPhone|iPad/i.test(navigator.userAgent);
  var renderer = new THREE.WebGLRenderer({ antialias: true, powerPreference: 'high-performance' });
  renderer.setPixelRatio(Math.min(window.devicePixelRatio || 1, IS_MOBILE ? 1.8 : 2));
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.shadowMap.enabled = true;
  renderer.shadowMap.type = THREE.PCFSoftShadowMap;
  renderer.toneMapping = THREE.ACESFilmicToneMapping;
  renderer.toneMappingExposure = 1.15;
  setRenderSRGB(renderer);
  document.body.appendChild(renderer.domElement);
  var MAX_ANISO = renderer.capabilities.getMaxAnisotropy();
  var scene = new THREE.Scene();
  scene.background = FOG_COLOR.clone();
  scene.fog = new THREE.Fog(FOG_COLOR.clone(), 110, 290);
  var camera = new THREE.PerspectiveCamera(58, window.innerWidth / window.innerHeight, 0.1, 900);
  camera.position.set(0, 5.8, 24);
  /* ===================== 天空（日夜 + 下雨） ===================== */
  var skyMaterial = null;
  (function () {
    skyMaterial = new THREE.ShaderMaterial({
      side: THREE.BackSide, depthWrite: false, fog: false,
      uniforms: {
        uTop:    { value: new THREE.Color(0x3a72b0) },
        uUpper:  { value: new THREE.Color(0x6ba0d0) },
        uMid:    { value: new THREE.Color(0xaccce8) },
        uBottom: { value: new THREE.Color(0xecf2f8) },
        uRain:   { value: 0 },
        uNight:  { value: 0 }
      },
      vertexShader: 'varying vec3 vPos; void main() { vPos = position; gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0); }',
      fragmentShader: [
        'uniform vec3 uTop; uniform vec3 uUpper; uniform vec3 uMid; uniform vec3 uBottom;',
        'uniform float uRain; uniform float uNight; varying vec3 vPos;',
        'void main() {',
        '  float h = normalize(vPos).y;',
        '  vec3 topR = mix(uTop, vec3(0.05,0.06,0.10), uNight);',
        '  vec3 upR  = mix(uUpper, vec3(0.08,0.10,0.15), uNight);',
        '  vec3 midR = mix(uMid, vec3(0.10,0.12,0.18), uNight);',
        '  vec3 botR = mix(uBottom, vec3(0.14,0.16,0.22), uNight);',
        '  vec3 topD = mix(topR, vec3(0.15,0.18,0.22), uRain * (1.0 - uNight));',
        '  vec3 upD  = mix(upR,  vec3(0.28,0.32,0.38), uRain * (1.0 - uNight));',
        '  vec3 midD = mix(midR, vec3(0.42,0.46,0.52), uRain * (1.0 - uNight));',
        '  vec3 botD = mix(botR, vec3(0.55,0.58,0.62), uRain * (1.0 - uNight));',
        '  vec3 col;',
        '  if (h > 0.35) col = mix(upD, topD, smoothstep(0.35, 0.9, h));',
        '  else if (h > 0.05) col = mix(midD, upD, smoothstep(0.05, 0.35, h));',
        '  else col = mix(botD, midD, smoothstep(-0.15, 0.05, h));',
        '  gl_FragColor = vec4(col, 1.0);',
        '  #include <tonemapping_fragment>',
        '  #include <encodings_fragment>',
        '}'
      ].join('\n')
    });
    scene.add(new THREE.Mesh(new THREE.SphereGeometry(450, 32, 16), skyMaterial));
  })();
  /* ===================== 星星（夜晚出现） ===================== */
  var starField = (function () {
    var COUNT = 500;
    var geo = new THREE.BufferGeometry();
    var positions = new Float32Array(COUNT * 3);
    var sizes = new Float32Array(COUNT);
    for (var i = 0; i < COUNT; i++) {
      var theta = Math.random() * Math.PI * 2;
      var phi = Math.random() * Math.PI * 0.45;
      var r = 400;
      positions[i*3] = r * Math.sin(phi) * Math.cos(theta);
      positions[i*3+1] = r * Math.cos(phi);
      positions[i*3+2] = r * Math.sin(phi) * Math.sin(theta);
      sizes[i] = 1 + Math.random() * 2.5;
    }
    geo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
    geo.setAttribute('size', new THREE.BufferAttribute(sizes, 1));
    var mat = new THREE.PointsMaterial({
      color: 0xffffff,
      size: 2.5,
      sizeAttenuation: false,
      transparent: true,
      opacity: 0,
      depthWrite: false,
      fog: false
    });
    var points = new THREE.Points(geo, mat);
    scene.add(points);
    return points;
  })();
  /* ===================== 太阳与月亮 ===================== */
  var sunGroup = new THREE.Group();
  (function () {
    var sunBall = new THREE.Mesh(new THREE.SphereGeometry(22, 20, 16), new THREE.MeshBasicMaterial({ color: 0xfff8d0, transparent: true, opacity: 0.95 }));
    sunGroup.add(sunBall);
    var halo1 = new THREE.Sprite(new THREE.SpriteMaterial({ color: 0xffeaa0, transparent: true, opacity: 0.28, depthWrite: false, blending: THREE.AdditiveBlending }));
    halo1.scale.set(120, 120, 1); sunGroup.add(halo1);
    var halo2 = new THREE.Sprite(new THREE.SpriteMaterial({ color: 0xffd070, transparent: true, opacity: 0.1, depthWrite: false, blending: THREE.AdditiveBlending }));
    halo2.scale.set(220, 220, 1); sunGroup.add(halo2);
    scene.add(sunGroup);
  })();
  var moonGroup = new THREE.Group();
  (function () {
    var moonBall = new THREE.Mesh(new THREE.SphereGeometry(16, 20, 16), new THREE.MeshBasicMaterial({ color: 0xe8eef8, transparent: true, opacity: 0.95 }));
    moonGroup.add(moonBall);
    var moonHalo = new THREE.Sprite(new THREE.SpriteMaterial({ color: 0xc8d8f0, transparent: true, opacity: 0.25, depthWrite: false, blending: THREE.AdditiveBlending }));
    moonHalo.scale.set(80, 80, 1); moonGroup.add(moonHalo);
    var craterMat = new THREE.MeshBasicMaterial({ color: 0xb8c0d0, transparent: true, opacity: 0.4 });
    var crater1 = new THREE.Mesh(new THREE.SphereGeometry(3, 8, 6), craterMat);
    crater1.position.set(8, 4, 4); moonGroup.add(crater1);
    var crater2 = new THREE.Mesh(new THREE.SphereGeometry(4, 8, 6), craterMat);
    crater2.position.set(-6, -3, 6); moonGroup.add(crater2);
    var crater3 = new THREE.Mesh(new THREE.SphereGeometry(2.5, 8, 6), craterMat);
    crater3.position.set(0, 8, -5); moonGroup.add(crater3);
    moonGroup.visible = false;
    scene.add(moonGroup);
  })();
  /* ===================== 光照 ===================== */
  var hemi = new THREE.HemisphereLight(0xe2eeff, 0x506e3a, 0.68);
  scene.add(hemi);
  var sun = new THREE.DirectionalLight(0xfff3d8, 2.8);
  sun.position.copy(SUN_DIR).multiplyScalar(140);
  sun.castShadow = true;
  sun.shadow.mapSize.set(IS_MOBILE ? 1024 : 2048, IS_MOBILE ? 1024 : 2048);
  sun.shadow.camera.left = -100; sun.shadow.camera.right = 100;
  sun.shadow.camera.top = 100; sun.shadow.camera.bottom = -100;
  sun.shadow.camera.near = 20; sun.shadow.camera.far = 360;
  sun.shadow.bias = -0.0004; sun.shadow.normalBias = 0.02;
  sun.shadow.radius = 4;
  scene.add(sun); scene.add(sun.target);
  var fillLight = new THREE.DirectionalLight(0xffe0b0, 0.38);
  fillLight.position.set(60, 30, -80);
  scene.add(fillLight);
  var ambient = new THREE.AmbientLight(0xa8c0d8, 0.18);
  scene.add(ambient);
  /* ===================== 云层 ===================== */
  var cloudMesh = (function () {
    var CLUSTERS = 30, PER = 8, COUNT = CLUSTERS * PER;
    var geo = new THREE.SphereGeometry(1, 10, 8);
    var mat = new THREE.MeshStandardMaterial({ color: 0xffffff, roughness: 1.0, flatShading: true, fog: false, transparent: true, opacity: 0.95 });
    var mesh = new THREE.InstancedMesh(geo, mat, COUNT);
    var m = new THREE.Matrix4(), p = new THREE.Vector3(), q = new THREE.Quaternion(), s = new THREE.Vector3();
    var idx = 0;
    for (var c = 0; c < CLUSTERS; c++) {
      var cx = (Math.random()-0.5) * 280, cy = 45 + Math.random() * 35, cz = (Math.random()-0.5) * 280;
      for (var k = 0; k < PER; k++) {
        var sc = 12 + Math.random() * 18;
        p.set(cx + (Math.random()-0.5)*30, cy + (Math.random()-0.5)*5, cz + (Math.random()-0.5)*30);
        q.identity();
        s.set(sc, sc * (0.35 + Math.random()*0.22), sc * (0.8 + Math.random()*0.4));
        m.compose(p, q, s); mesh.setMatrixAt(idx++, m);
      }
    }
    mesh.instanceMatrix.needsUpdate = true;
    scene.add(mesh); return mesh;
  })();
  /* ===================== 雨系统 ===================== */
  var weather = {
    raining: false,
    rainIntensity: 0,
    targetIntensity: 0,
    changeTimer: 100 + Math.random() * 80
  };
  var RAIN_COUNT = IS_MOBILE ? 500 : 800;
  var RAIN_RADIUS = 45;
  var rainMesh = (function () {
    var geo = new THREE.BoxGeometry(0.012, 0.42, 0.012);
    var mat = new THREE.MeshBasicMaterial({ color: 0xc8d8e8, transparent: true, opacity: 0.42, depthWrite: false });
    var mesh = new THREE.InstancedMesh(geo, mat, RAIN_COUNT);
    mesh.instanceMatrix.setUsage(THREE.DynamicDrawUsage);
    mesh.frustumCulled = false;
    scene.add(mesh);
    return mesh;
  })();
  var rainParticles = [];
  var _rainMatrix = new THREE.Matrix4();
  var _rainQuat = new THREE.Quaternion();
  var _rainEuler = new THREE.Euler(0, 0, 0.12);
  var _rainScale = new THREE.Vector3(1, 1, 1);
  var _rainPos = new THREE.Vector3();
  function initRainParticle(i) {
    rainParticles[i] = {
      x: player.pos.x + (Math.random() - 0.5) * RAIN_RADIUS * 2,
      z: player.pos.z + (Math.random() - 0.5) * RAIN_RADIUS * 2,
      y: 8 + Math.random() * 28,
      vy: 22 + Math.random() * 14,
      vx: -1.5,
      vz: 0
    };
  }
  function reinitAllRain() {
    for (var i = 0; i < RAIN_COUNT; i++) initRainParticle(i);
  }
  var rainInitialized = false;
  /* ===================== 草地贴图 ===================== */
  function makeGrassTexture() {
    var S = 1024; var cv = document.createElement('canvas'); cv.width = cv.height = S;
    var ctx = cv.getContext('2d');
    ctx.fillStyle = '#4a7038'; ctx.fillRect(0, 0, S, S);
    function blob(x, y, r, hue, sat, lig, alpha) {
      for (var ox = -1; ox <= 1; ox++) for (var oy = -1; oy <= 1; oy++) {
        var px = x + ox * S, py = y + oy * S;
        if (px < -r || px > S + r || py < -r || py > S + r) continue;
        var g = ctx.createRadialGradient(px, py, 0, px, py, r);
        g.addColorStop(0, 'hsla(' + hue + ',' + sat + '%,' + lig + '%,' + alpha + ')');
        g.addColorStop(1, 'hsla(' + hue + ',' + sat + '%,' + lig + '%,0)');
        ctx.fillStyle = g; ctx.beginPath(); ctx.arc(px, py, r, 0, Math.PI * 2); ctx.fill();
      }
    }
    for (var i = 0; i < 220; i++) blob(Math.random()*S, Math.random()*S, 30+Math.random()*160, 88+Math.random()*28, 34+Math.random()*28, 22+Math.random()*16, 0.5);
    for (var j = 0; j < 15000; j++) {
      var x = Math.random()*S, y = Math.random()*S, l = 22+Math.random()*32;
      ctx.fillStyle = 'hsla(' + (95+Math.random()*30) + ',50%,' + l + '%,0.5)';
      ctx.fillRect(x, y, 2, 2+Math.random()*5);
    }
    var tex = new THREE.CanvasTexture(cv);
    tex.wrapS = tex.wrapT = THREE.RepeatWrapping; tex.repeat.set(0.062, 0.062);
    tex.anisotropy = MAX_ANISO; setTexSRGB(tex); return tex;
  }
  /* ===================== 地面 ===================== */
  (function () {
    var shape = new THREE.Shape();
    shape.absellipse(0, 0, CFG.groundA, CFG.groundB, 0, Math.PI * 2, false, 0);
    var hole = new THREE.Path();
    hole.absellipse(0, -CFG.lakeZ, CFG.rimA, CFG.rimB, 0, Math.PI * 2, true, 0);
    shape.holes.push(hole);
    var geo = new THREE.ShapeGeometry(shape, 64); geo.rotateX(-Math.PI / 2);
    var mat = new THREE.MeshStandardMaterial({ map: makeGrassTexture(), roughness: 0.93, metalness: 0.0 });
    var ground = new THREE.Mesh(geo, mat); ground.receiveShadow = true; scene.add(ground);
  })();
  /* ===================== 湖岸斜坡 ===================== */
  function createRingGeometry(aOut, bOut, yOut, aIn, bIn, yIn, cx, cz, seg) {
    seg = seg || 128; var positions = [], uvs = [], indices = [];
    for (var i = 0; i <= seg; i++) {
      var t = (i / seg) * Math.PI * 2, c = Math.cos(t), s = Math.sin(t);
      positions.push(cx + aOut*c, yOut, cz + bOut*s); uvs.push(i/seg, 1);
      positions.push(cx + aIn*c, yIn, cz + bIn*s); uvs.push(i/seg, 0);
    }
    for (var k = 0; k < seg; k++) {
      var o0=k*2, i0=k*2+1, o1=(k+1)*2, i1=(k+1)*2+1;
      indices.push(o0,i0,i1,o0,i1,o1);
    }
    var geo = new THREE.BufferGeometry();
    geo.setAttribute('position', new THREE.Float32BufferAttribute(positions, 3));
    geo.setAttribute('uv', new THREE.Float32BufferAttribute(uvs, 2));
    geo.setIndex(indices); geo.computeVertexNormals(); return geo;
  }
  (function () {
    var geo = createRingGeometry(CFG.rimA, CFG.rimB, 0.02, 30, 12, -2.5, CFG.lakeX, CFG.lakeZ, 128);
    var mat = new THREE.MeshStandardMaterial({ color: 0xd2b890, roughness: 1.0, metalness: 0.0, side: THREE.DoubleSide });
    var beach = new THREE.Mesh(geo, mat); beach.receiveShadow = true; scene.add(beach);
  })();
  /* ===================== 湖水 ===================== */
  var waterUniforms = {
    uTime:     { value: 0 },
    uDeep:     { value: new THREE.Color(0x0e4258) },
    uShallow:  { value: new THREE.Color(0x3a98a8) },
    uSky:      { value: new THREE.Color(0xb0e0f0) },
    uSunDir:   { value: SUN_DIR.clone() },
    uSunColor: { value: new THREE.Color(0xfff5dc) },
    uCenter:   { value: new THREE.Vector2(CFG.lakeX, CFG.lakeZ) },
    uRad:      { value: new THREE.Vector2(34.2, 16.2) },
    uNight:    { value: 0 }
  };
  (function () {
    var shape = new THREE.Shape();
    shape.absellipse(0, -CFG.lakeZ, 34.2, 16.2, 0, Math.PI * 2, false, 0);
    var geo = new THREE.ShapeGeometry(shape, 96); geo.rotateX(-Math.PI / 2);
    var mat = new THREE.ShaderMaterial({
      uniforms: waterUniforms, fog: true,
      vertexShader: [
        'varying vec3 vWorldPos; varying float vFogDepth;',
        'void main() {',
        '  vec4 wp = modelMatrix * vec4(position, 1.0);',
        '  vWorldPos = wp.xyz;',
        '  vec4 mvPosition = viewMatrix * wp;',
        '  vFogDepth = -mvPosition.z;',
        '  gl_Position = projectionMatrix * mvPosition;',
        '}'
      ].join('\n'),
      fragmentShader: [
        'uniform float uTime; uniform float uNight;',
        'uniform vec3 uDeep; uniform vec3 uShallow; uniform vec3 uSky;',
        'uniform vec3 uSunDir; uniform vec3 uSunColor;',
        'uniform vec2 uCenter; uniform vec2 uRad;',
        'uniform vec3 fogColor; uniform float fogNear; uniform float fogFar;',
        'varying vec3 vWorldPos; varying float vFogDepth;',
        'void main() {',
        '  vec2 p = vWorldPos.xz;',
        '  float w  = sin(p.x * 0.45 + uTime * 1.10) * 0.50;',
        '        w += sin(p.y * 0.52 - uTime * 0.90) * 0.50;',
        '        w += sin((p.x + p.y) * 0.31 + uTime * 0.70) * 0.40;',
        '        w += sin((p.x - p.y * 0.7) * 0.83 - uTime * 1.60) * 0.22;',
        '        w += sin((p.x * 1.3 + p.y * 1.7) * 1.1 + uTime * 2.1) * 0.08;',
        '  vec3 N = normalize(vec3(w * 0.085, 1.0, w * 0.065));',
        '  vec3 V = normalize(cameraPosition - vWorldPos);',
        '  float fres = pow(1.0 - clamp(dot(N, V), 0.0, 1.0), 3.5);',
        '  vec2 q = vec2((p.x - uCenter.x) / uRad.x, (p.y - uCenter.y) / uRad.y);',
        '  float d = clamp(length(q), 0.0, 1.0);',
        '  vec3 deepNight = mix(uDeep, vec3(0.02,0.03,0.06), uNight);',
        '  vec3 shalNight = mix(uShallow, vec3(0.03,0.05,0.08), uNight);',
        '  vec3 skyNight = mix(uSky, vec3(0.05,0.06,0.10), uNight);',
        '  vec3 base = mix(deepNight, shalNight, smoothstep(0.10, 0.95, d));',
        '  vec3 col = mix(base, skyNight, fres * 0.88);',
        '  vec3 H = normalize(uSunDir + V);',
        '  float spec = pow(max(dot(N, H), 0.0), 280.0);',
        '  col += uSunColor * spec * 3.0 * (1.0 - uNight * 0.8);',
        '  col += uSunColor * 0.1 * smoothstep(0.55, 1.0, w * 0.5 + 0.5) * fres * (1.0 - uNight * 0.8);',
        '  float fogFactor = smoothstep(fogNear, fogFar, vFogDepth);',
        '  col = mix(col, fogColor, fogFactor);',
        '  gl_FragColor = vec4(col, 1.0);',
        '  #include <tonemapping_fragment>',
        '  #include <encodings_fragment>',
        '}'
      ].join('\n')
    });
    var water = new THREE.Mesh(geo, mat);
    water.position.y = CFG.waterY; water.renderOrder = 1; scene.add(water);
  })();
  /* ===================== 水藻（岸边） ===================== */
  (function () {
    var COUNT = IS_MOBILE ? 220 : 320;
    var geo = new THREE.ConeGeometry(0.35, 0.9, 4);
    geo.translate(0, 0.45, 0);
    var mat = new THREE.MeshStandardMaterial({ color: 0xffffff, roughness: 0.95, flatShading: true });
    var mesh = new THREE.InstancedMesh(geo, mat, COUNT);
    mesh.castShadow = true;
    var m = new THREE.Matrix4(), p = new THREE.Vector3(), q = new THREE.Quaternion(), e = new THREE.Euler(), s = new THREE.Vector3(), col = new THREE.Color();
    for (var i = 0; i < COUNT; i++) {
      var t = Math.random() * Math.PI * 2;
      var rr = 1.0 + Math.random() * 0.08;
      var x = CFG.lakeX + CFG.rimA * rr * Math.cos(t);
      var z = CFG.lakeZ + CFG.rimB * rr * Math.sin(t);
      var sc = 0.6 + Math.random() * 0.9;
      p.set(x, CFG.waterY + 0.05, z);
      e.set((Math.random()-0.5)*0.3, Math.random()*Math.PI*2, (Math.random()-0.5)*0.3);
      q.setFromEuler(e);
      s.set(sc * 0.6, sc, sc * 0.6);
      m.compose(p, q, s); mesh.setMatrixAt(i, m);
      col.setHSL(0.24+Math.random()*0.08, 0.35+Math.random()*0.25, 0.2+Math.random()*0.12);
      mesh.setColorAt(i, col);
    }
    mesh.instanceMatrix.needsUpdate = true;
    if (mesh.instanceColor) mesh.instanceColor.needsUpdate = true;
    scene.add(mesh);
  })();
  /* ===================== 浮萍（湖中漂浮） ===================== */
  (function () {
    var COUNT = IS_MOBILE ? 120 : 200;
    var geo = new THREE.CircleGeometry(0.35, 6);
    geo.rotateX(-Math.PI / 2);
    var mat = new THREE.MeshStandardMaterial({ color: 0xffffff, roughness: 0.85, side: THREE.DoubleSide, transparent: true, opacity: 0.9 });
    var mesh = new THREE.InstancedMesh(geo, mat, COUNT);
    var m = new THREE.Matrix4(), p = new THREE.Vector3(), q = new THREE.Quaternion(), e = new THREE.Euler(), s = new THREE.Vector3(), col = new THREE.Color();
    for (var i = 0; i < COUNT; i++) {
      var t = Math.random() * Math.PI * 2;
      var rr = Math.sqrt(Math.random()) * 0.9;
      var x = C
