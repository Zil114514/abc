<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>小冉治愈物语</title>
<style>
*{box-sizing:border-box;margin:0;padding:0;font-family:"Microsoft Yahei",sans-serif;}
body{background:#1a1a27;color:#eee;padding:12px;max-width:720px;margin:0 auto;font-size:16px;}
#log{background:#222738;padding:14px;border-radius:10px;min-height:260px;max-height:60vh;overflow-y:auto;margin-bottom:12px;white-space:pre-line;line-height:1.7;}
.btn-wrap{display:grid;grid-template-columns:1fr 1fr;gap:8px;margin-bottom:10px;}
button{padding:10px 6px;border:none;border-radius:8px;background:#4060c0;color:#fff;font-size:15px;cursor:pointer;}
button:active{background:#284090;}
.info{background:#2b3044;padding:10px;border-radius:8px;margin-bottom:10px;}
input{padding:8px;border-radius:6px;border:0;width:100%;margin:6px 0;font-size:16px;}
</style>
</head>
<body>
<div class="info">
<div id="status"></div>
</div>
<div id="log"></div>
<div id="input-area"></div>
<div class="btn-wrap" id="btnArea"></div>

<script>
const SAVE_KEY = "xiaoran_save";
let player = {};
let bag = [];

//物品库
const item_data = {
    1: {"name":"牛奶", "price":20, "trust":9999, "desc":"温热牛奶，小冉喜欢"},
    2: {"name":"草莓蛋糕", "price":50, "trust":9999, "desc":"小冉最爱甜品"},
    3: {"name":"精致手链", "price":150, "trust":9999, "desc":"精致饰品，好感大幅提升"},
    4: {"name":"星空礼盒", "price":200, "trust":9999, "desc":"超级稀有礼物"},
    5: {"name":"治愈药水", "price":30, "heal":40, "trust":0, "desc":"恢复生命值，不能增加信赖"}
};

//DOM工具
const logDiv = document.getElementById("log");
const statusDiv = document.getElementById("status");
const btnArea = document.getElementById("btnArea");
const inputArea = document.getElementById("input-area");

function sleep(ms){return new Promise(res=>setTimeout(res,ms));}
function randInt(a,b){return Math.floor(Math.random()*(b-a+1))+a;}

function print(text){
    logDiv.innerText += text+"\n";
    logDiv.scrollTop = logDiv.scrollHeight;
}
function clearLog(){logDiv.innerText="";}
function clearBtn(){btnArea.innerHTML="";inputArea.innerHTML="";}

//更新状态栏
function renderStatus(){
    const [lv,lvName] = getTrustLevel();
    statusDiv.innerText = `主角：${player.name}｜第${player.day}天 ${player.time}\n信任：${player.trust}(${lvName})｜金币：${player.gold}｜体力：${player.hp}/${player.max_hp}`;
}

//信任等级
function getTrustLevel(){
    const t = player.trust;
    if(t<10) return [0,"陌生"];
    else if(t<40) return [1,"普通朋友"];
    else if(t<100) return [2,"亲密好友"];
    else if(t<200) return [3,"非常依赖"];
    else return [4,"全心信任"];
}

//初始化
function initGameData(playerName="主角"){
    return {
        name:playerName,
        gold:300,
        hp:100,
        max_hp:100,
        trust:0,
        day:1,
        time:"白天",
        end_flag:0
    }
}

//存档 localStorage
function saveGame(){
    const data = {player,bag};
    localStorage.setItem(SAVE_KEY,JSON.stringify(data));
    print("\n✅ 存档成功！");
}
function loadGame(){
    const str = localStorage.getItem(SAVE_KEY);
    if(!str) return null;
    return JSON.parse(str);
}

//切换昼夜
async function changeTime(){
    if(player.time === "白天"){
        player.time = "夜晚";
        print("\n🌙 天色变暗，进入夜晚");
    }else{
        player.time = "白天";
        player.day +=1;
        print(`\n☀️ 新的一天！第${player.day}天`);
    }
    renderStatus();
    await sleep(1000);
    showMainMenu();
}

//打工系统
async function workJob(){
    clearLog();
    print("\n💼 打工中心（仅白天可以打工，打工消耗体力）");
    print(`当前体力：${player.hp}`);
    print("--------------------------------");
    clearBtn();
    if(player.time === "夜晚"){
        print("\n❌ 已经是夜晚，店铺关门，无法打工！");
        await sleep(1200);
        showMainMenu();
        return;
    }
    function doWork(jobIdx){
        clearBtn();
        let jobName,hpCost,goldGet;
        if(jobIdx===1){
            jobName="便利店兼职";hpCost=20;goldGet=randInt(80000,120000);
        }else if(jobIdx===2){
            jobName="餐厅服务生";hpCost=30;goldGet=randInt(130,180);
        }else if(jobIdx===3){
            jobName="搬运临时工";hpCost=45;goldGet=randInt(200,280);
        }else{
            showMainMenu();
            return;
        }
        if(player.hp < hpCost){
            print(`\n❌ 体力不足！需要${hpCost}点体力，现在只有${player.hp}`);
            setTimeout(()=>showMainMenu(),1200);
            return;
        }
        print(`\n⏳ 开始【${jobName}】...`);
        setTimeout(()=>{
            player.hp -= hpCost;
            player.gold += goldGet;
            print(`✅ 打工结束！体力-${hpCost}，金币+${goldGet}`);
            renderStatus();
            setTimeout(()=>showMainMenu(),1200);
        },1500);
    }
    btnArea.innerHTML += `<button onclick="doWork(1)">1.便利店兼职</button>`;
    btnArea.innerHTML += `<button onclick="doWork(2)">2.餐厅服务生</button>`;
    btnArea.innerHTML += `<button onclick="doWork(3)">3.搬运临时工</button>`;
    btnArea.innerHTML += `<button onclick="showMainMenu()">0.离开</button>`;
    window.doWork = doWork;
}

//商店
async function shop(){
    clearLog();
    print("\n🏪 礼物商店");
    print(`你的金币：${player.gold}`);
    print("--------------------------------");
    for(const [k,item] of Object.entries(item_data)){
        print(`${k}. ${item.name}｜价格:${item.price}｜${item.desc}`);
    }
    print("0.离开商店");
    clearBtn();
    function buyItem(id){
        if(id===0){showMainMenu();return;}
        const it = item_data[id];
        if(player.gold >= it.price){
            player.gold -= it.price;
            bag.push(Number(id));
            print(`\n✅ ${player.name}购买了【${it.name}】！已放进背包。`);
        }else{
            print(`\n❌金币不足，还差${it.price-player.gold}`);
        }
        renderStatus();
        setTimeout(()=>shop(),1200);
    }
    for(const k of Object.keys(item_data)){
        btnArea.innerHTML += `<button onclick="buyItem(${k})">${k}.${item_data[k].name}</button>`;
    }
    btnArea.innerHTML += `<button onclick="showMainMenu()">0.离开</button>`;
    window.buyItem = buyItem;
}

//赠送礼物
async function giveGift(){
    clearLog();
    if(bag.length===0){
        print("\n🎁 背包空空，没有礼物可以赠送！");
        await sleep(1200);
        showMainMenu();
        return;
    }
    print("\n🎁 打开背包，选择要送给小冉的礼物");
    const unique_bag = {};
    for(let iid of bag){
        const it = item_data[iid];
        if(unique_bag[iid]) unique_bag[iid].count+=1;
        else unique_bag[iid] = {name:it.name,count:1,trust:it.trust||0};
    }
    const idList = Object.keys(unique_bag);
    let num=1;
    for(let iid of idList){
        const info = unique_bag[iid];
        print(`${num}. ${info.name} ×${info.count}  信赖+${info.trust}`);
        num++;
    }
    clearBtn();
    function selectGift(idx){
        if(idx===-1){showMainMenu();return;}
        const giftId = Number(idList[idx]);
        const giftInfo = item_data[giftId];
        const pos = bag.indexOf(giftId);
        bag.splice(pos,1);
        print(`\n${player.name}：这个送给你。`);
        setTimeout(()=>{
            print(`小冉：哇！是【${giftInfo.name}】，谢谢你！`);
            const addT = giftInfo.trust||0;
            if(addT>0){
                player.trust += addT;
                print(`\n💖 信赖值 +${addT}！`);
            }else{
                print("\n这个物品不能增加信赖。");
            }
            renderStatus();
            setTimeout(()=>showMainMenu(),1400);
        },1000);
    }
    for(let i=0;i<idList.length;i++){
        btnArea.innerHTML += `<button onclick="selectGift(${i})">${i+1}.${unique_bag[idList[i]].name}</button>`;
    }
    btnArea.innerHTML += `<button onclick="selectGift(-1)">取消</button>`;
    window.selectGift = selectGift;
}

//旅游
async function travelWithXiaoran(){
    const [level] = getTrustLevel();
    clearLog();
    if(player.hp <20){
        print("\n😥 体力不足，暂时不能出去旅游！");
        await sleep(1200);
        showMainMenu();
        return;
    }
    print("\n🌏【双人旅行｜选择想去的地方】");
    const travel_story = {
        "1":{title:"大理洱海",lines:[[`${player.name}`,"我笑着对小冉说：今天带你去大理洱海吹风。"],["小冉","哇……洱海好漂亮，湖水好干净！"],["小冉","风吹过湖面，带着淡淡的草木香气。"],["小冉",`和${player.name}一起环湖散步，真的好治愈～`],["小冉","风软软的，有你在身边真好❤️"]]},
        "2":{title:"圣托里尼",lines:[[`${player.name}`,"我牵着小冉的手：带你来看全世界最浪漫的蓝白小镇。"],["小冉","蓝顶教堂配大海……美得像童话！"],["小冉","放眼望去，海和天空连成一片。"],["小冉",`谢谢你，${player.name}，带我来这里。`],["小冉",`和${player.name}看爱琴海，是最幸福的事情✨`]]},
        "3":{title:"冰岛极光",lines:[[`${player.name}`,"我轻声道：带你看世间最浪漫的极光。"],["小冉","（瞪大双眼）极光……真的太梦幻了！"],["小冉","漫天绿光在夜空缓缓跳动。"],["小冉","我一辈子都忘不掉此刻的景色！"],["小冉",`谢谢你陪我看这么美的风景，${player.name}❤️`]]},
        "4":{title:"九寨沟",lines:[[`${player.name}`,"我牵着她漫步栈道：这里就是人间童话。"],["小冉","湖水五颜六色的，好像大自然的调色盘！"],["小冉","周围的森林安安静静，只有水流声。"],["小冉","这里几乎没有别的游客，好像只有我们两个人～"],["小冉",`和${player.name}走在这里，心都变柔软了。`]]},
        "5":{title:"东山岛",lines:[[`${player.name}`,"我坐在沙滩上：陪你看一场温柔的海边日落。"],["小冉","海边的晚风轻轻吹过来，好舒服呀～"],["小冉","整片天空都被染成橘红色。"],["小冉","海浪一下一下拍在沙滩上，安静又浪漫。"],["小冉",`和${player.name}看海，是我最开心的时刻✨`]]},
        "6":{title:"阳朔小镇",lines:[[`${player.name}`,"我慢慢散步：带你感受安静温柔的山水小城。"],["小冉","连绵的山水环绕小镇，这里真的好安静。"],["小冉","河水缓缓流淌，空气中都是青草味道。"],["小冉","不用赶路，慢慢闲逛的感觉真好。"],["小冉",`和${player.name}独处的时光，我最珍惜❤️`]]}
    };
    clearBtn();
    function doTravel(ch){
        if(ch==="7"){showMainMenu();return;}
        if(!travel_story[ch]){showMainMenu();return;}
        clearLog();
        const data = travel_story[ch];
        print(`\n🌏 出发前往【${data.title}】`);
        player.hp -=20;
        const addTr = randInt(25,45);
        player.trust += addTr;
        renderStatus();
        async function showLines(){
            for(let [sp,tx] of data.lines){
                print(`${sp}：${tx}`);
                await sleep(1200);
            }
            print(`\n💖双人旅行结束！信任大幅 +${addTr}，体力 -20`);
            setTimeout(()=>showMainMenu(),1400);
        }
        showLines();
    }
    btnArea.innerHTML +=`<button onclick="doTravel('1')">1.大理洱海</button>`;
    btnArea.innerHTML +=`<button onclick="doTravel('2')">2.圣托里尼</button>`;
    btnArea.innerHTML +=`<button onclick="doTravel('3')">3.冰岛极光</button>`;
    btnArea.innerHTML +=`<button onclick="doTravel('4')">4.九寨沟</button>`;
    btnArea.innerHTML +=`<button onclick="doTravel('5')">5.东山岛</button>`;
    btnArea.innerHTML +=`<button onclick="doTravel('6')">6.阳朔小镇</button>`;
    btnArea.innerHTML +=`<button onclick="doTravel('7')">7.取消</button>`;
    window.doTravel = doTravel;
}

//求婚
async function proposeMarry(){
    const [level] = getTrustLevel();
    clearLog();
    print("\n💍 你深吸一口气，准备向小冉告白求婚...");
    await sleep(1200);
    if(level <4 || player.day <7){
        print(`${player.name}：小冉，我有很重要的话想对你说...`);
        await sleep(1000);
        let lines;
        if(level <=2){
            lines = [["小冉",`${player.name}？……怎么突然这么认真？`],[`${player.name}`,"我喜欢你，想和你永远在一起。"],["小冉","（脸红低头）唔……谢谢你喜欢我。"],["小冉","但是我们现在还太早啦，再慢慢相处好不好？🥺"],["旁白","💔 好感不足，小冉害羞婉拒了你。"]];
            player.trust +=5;
        }else{
            lines = [["小冉",`${player.name}，你今天好奇怪，怎么突然这么严肃？`],[`${player.name}`,"我想永远和你在一起。"],["小冉","我也很依赖你……"],["小冉","但我还想再多和你相处一阵子，不想这么仓促～"],["旁白","💫 小冉很喜欢你，但还没准备好结婚！"]];
            player.trust +=10;
        }
        renderStatus();
        for(let [sp,tx] of lines){
            print(`${sp}：${tx}`);
            await sleep(1000);
        }
        setTimeout(()=>showMainMenu(),1400);
        return;
    }
    const successLines = [
        [`${player.name}`,"小冉，从认识你的第一天开始，"],
        [`${player.name}`,"我就希望能一辈子陪着你、保护你。"],
        [`${player.name}`,"你的温柔、你的可爱、你的所有样子，我都超级喜欢。"],
        [`${player.name}`,"我不想只做你的朋友。"],
        [`${player.name}`,"我想成为你一辈子的依靠。"],
        [`${player.name}`,"小冉，嫁给我，好吗？💍"],
        ["旁白","\n😭 小冉瞬间红了眼眶，呆呆看着你..."],
        ["小冉",`${player.name}……你是认真的吗？`],
        [`${player.name}`,"无比认真，余生只想是你。"],
        ["小冉","（落泪微笑）我一直在等你这句话……❤️"],
        ["小冉",`${player.name}！我愿意！我愿意嫁给你！！`]
    ];
    for(let [sp,tx] of successLines){
        print(`${sp}：${tx}`);
        await sleep(1300);
    }
    player.end_flag =2;
    print("\n🎉🎉🎉 达成结婚结局！游戏通关！");
}

//猜拳
async function rockPaperScissors(){
    const [level] = getTrustLevel();
    clearLog();
    const game_dialog = [
        "小冉：石头剪刀布吗……我不太常玩，不过可以试试。",
        "小冉：猜拳？听起来很有趣，那我们来玩玩！",
        "小冉：好呀！我想和你比试看看，我不会手下留情的哦~",
        `小冉：太好了！只要是和${player.name}一起玩，我都很开心❤️`,
        `小冉：太棒啦！最喜欢和${player.name}一起玩游戏，我们快开始！`
    ];
    print(`\n✊ ${player.name}笑着对小冉提议：我们来玩石头剪刀布好不好？`);
    await sleep(800);
    print(game_dialog[level]);
    await sleep(1000);
    print("\n===== 石头剪刀布 =====");
    print("1石头｜2剪刀｜3布，三局两胜");
    let pWin=0,xWin=0;
    const nameMap = {1:"石头",2:"剪刀",3:"布"};
    async function round(){
        if(pWin>=2 || xWin>=2){
            let addTr;
            if(pWin===2){
                addTr = randInt(14,22);
                print(`\n🎉 三局结束，最终是${player.name}获胜了！`);
                print(`${player.name}：哈哈，我赢咯！`);
                await sleep(800);
                print(`小冉：呜呜，我输啦，${player.name}好厉害！下次我一定要赢回来✨`);
            }else{
                addTr = randInt(6,12);
                print("\n😆 三局结束，小冉取得胜利！");
                print(`${player.name}：哇，居然被你赢了，厉害啊。`);
                await sleep(800);
                print(`小冉：嘿嘿，我赢啦！能和${player.name}玩真的好开心。`);
            }
            player.trust += addTr;
            renderStatus();
            print(`✊ 猜拳游戏结束！信任 +${addTr}`);
            setTimeout(()=>showMainMenu(),1400);
            return;
        }
        print(`\n当前比分：${player.name} ${pWin} ｜ 小冉 ${xWin}`);
        clearBtn();
        function selectHand(pc){
            clearBtn();
            const xc = randInt(1,3);
            print(`${player.name}：${nameMap[pc]}`);
            setTimeout(()=>{
                print(`小冉：${nameMap[xc]}`);
                setTimeout(()=>{
                    if(pc===xc){
                        print("平局！再来一局。");
                    }else if((pc===1&&xc===2)||(pc===2&&xc===3)||(pc===3&&xc===1)){
                        print(`${player.name}赢下这一局！`);pWin++;
                    }else{
                        print("小冉赢下这一局！");xWin++;
                    }
                    setTimeout(round,1100);
                },500);
            },500);
        }
        btnArea.innerHTML +=`<button onclick="selectHand(1)">1.石头</button>`;
        btnArea.innerHTML +=`<button onclick="selectHand(2)">2.剪刀</button>`;
        btnArea.innerHTML +=`<button onclick="selectHand(3)">3.布</button>`;
        window.selectHand = selectHand;
    }
    round();
}

//猜数字
async function playGameWithXiaoran(){
    const [level] = getTrustLevel();
    clearLog();
    const game_dialog = [
        "小冉：啊？游戏吗……我不太擅长，不过可以试试。",
        "小冉：游戏？听起来挺有意思，那我们玩玩看吧！",
        "小冉：好呀！能和你一起玩游戏，我很期待~",
        `小冉：太棒啦！只要是和${player.name}一起玩，我都很开心❤️`,
        `小冉：太好了！最喜欢和${player.name}一起玩耍了，我们快开始！`
    ];
    print(`\n🎲 ${player.name}笑着提议：我们一起来玩个小游戏好不好？`);
    await sleep(800);
    print(game_dialog[level]);
    await sleep(1000);
    print("\n===== 猜数字游戏 =====");
    print("小冉心里想好1~10之间数字，你来猜猜看！");
    const secret = randInt(1,10);
    let guessLeft =3;
    async function guessStep(){
        if(guessLeft<=0){
            const add = randInt(5,10);
            print(`\n😥 机会用完啦，正确数字是${secret}`);
            print(`${player.name}：哎呀，可惜猜错了。`);
            await sleep(800);
            print(`小冉：没关系！和${player.name}一起玩就很快乐啦。`);
            player.trust += add;
            renderStatus();
            print(`🎲 游玩结束！信任 +${add}`);
            setTimeout(()=>showMainMenu(),1400);
            return;
        }
        print(`\n还有 ${guessLeft} 次机会。请输入1‑10数字`);
        clearBtn();
        for(let i=1;i<=10;i++){
            btnArea.innerHTML +=`<button onclick="doGuess(${i})">${i}</button>`;
        }
        window.doGuess = async function(ans){
            clearBtn();
            if(ans === secret){
                const add = randInt(12,20);
                print(`\n🎉 ${player.name}猜对啦！`);
                print(`${player.name}：哈哈，我猜对咯！`);
                await sleep(800);
                print(`小冉：哇！${player.name}好厉害，恭喜你赢啦，好开心✨`);
                player.trust += add;
                renderStatus();
                print(`🎲 游玩结束！信任 +${add}`);
                setTimeout(()=>showMainMenu(),1400);
                return;
            }else if(ans < secret){
                print(`小冉：猜小咯，${player.name}，再试试更大一点的数字~`);
            }else{
                print(`小冉：猜大啦，往小一点猜猜看！`);
            }
            guessLeft -=1;
            setTimeout(guessStep,900);
        }
    }
    guessStep();
}

//亲密陪伴
async function sweetInteract(){
    const [level] = getTrustLevel();
    clearLog();
    print(`\n💗 ${player.name}选择温柔陪伴小冉...`);
    await sleep(800);
    const add = randInt(8,15);
    player.trust += add;
    const dialog_list = [
        [
            [`${player.name}`,"我轻轻靠近她，柔声开口：你看起来有点孤单。"],
            ["小冉",`${player.name}！……谢谢你这么温柔对我，有点害羞。`],
            [`${player.name}`,"我放慢语速：不用紧张，我只是想安安静静陪你一会。"],
            ["小冉","啊……不要靠这么近，我心跳变快了。"],
            [`${player.name}`,"我轻声说：如果觉得不舒服，随时可以告诉我。"],
            ["小冉","很少有人愿意这样温柔对待我，谢谢你。"]
        ],
        [
            [`${player.name}`,"我坐在她身旁：一个人在这里发呆吗？"],
            ["小冉",`被${player.name}陪着，我感觉很温暖。`],
            [`${player.name}`,"我望着远方景色：这样安静待着也挺好。"],
            ["小冉","这样安静待在一起，感觉很舒服呢。"],
            [`${player.name}`,"我缓缓说道：有机会，我可以经常过来陪你。"],
            ["小冉","风轻轻吹过来，有你在身边真好。"]
        ],
        [
            [`${player.name}`,"我看着她，微笑道：每次见到你，心情都会变好。"],
            ["小冉",`每次和${player.name}近距离相处，我都会很安心～`],
            [`${player.name}`,"我轻声说道：我很喜欢和你独处的时光。"],
            ["小冉","我喜欢这种安静的陪伴，不用刻意说话。"],
            [`${player.name}`,"我看着她的眼睛：只要你需要，我都会在。"],
            ["小冉","只要你在旁边，所有烦恼好像都消失了。"]
        ],
        [
            [`${player.name}`,"我轻轻对她说：我越来越在意你了。"],
            ["小冉",`我越来越离不开你了，${player.name}，真的很信任你❤️`],
            [`${player.name}`,"我认真开口：你的喜怒哀乐，我都想了解。"],
            ["小冉","和你靠近的时候，心里甜甜的。"],
            [`${player.name}`,"我低声说道：希望我们能一直这样相处下去。"],
            ["小冉","希望这样的时光，可以再久一点。"]
        ],
        [
            [`${player.name}`,"我温柔的看着她：在我面前不用伪装，做自己就好。"],
            ["小冉",`只有在${player.name}身边，我才敢这么放松呀～最喜欢你了！`],
            [`${player.name}`,"我说道：不管发生什么事，我都会认真倾听你的心事。"],
            ["小冉","我愿意把所有心事，都讲给你一个人听。"],
            [`${player.name}`,"我轻声许诺：往后的日子，我想一直陪着你。"],
            ["小冉",`往后的每一天，都想和${player.name}一起度过。`]
        ]
    ];
    const selected = dialog_list[level];
    for(let [sp,tx] of selected){
        print(`${sp}：${tx}`);
        await sleep(900);
    }
    print(`\n💖 亲密陪伴成功！信任 +${add}`);
    renderStatus();
    setTimeout(()=>showMainMenu(),1200);
}

//普通聊天
async function chatWithXiaoran(){
    const [level] = getTrustLevel();
    clearLog();
    print(`\n---和小冉聊天---`);
    const add = randInt(3,8);
    player.trust += add;
    renderStatus();
    const day_dialog = [
        [`${player.name}`,"今天外面天气不错，你经常来这里散步吗？","小冉：今天天气很好，我偶尔会来散步。"],
        [`${player.name}`,"路边这些小花很好看，你有注意到吗？","小冉：是的，刚刚我看了好久。"],
        [`${player.name}`,"阳光暖洋洋的，在这里休息很舒服。","小冉：白天的阳光暖暖的，晒着很舒服。"]
    ];
    const night_dialog = [
        [`${player.name}`,"今晚天上星星好多，你经常晚上来这里？","小冉：天上星星好多，我经常一个人看星星。"],
        [`${player.name}`,"夜里是不是容易胡思乱想？","小冉：夜晚很安静，容易胡思乱想。"],
        [`${player.name}`,"晚上风有点凉，要注意保暖。","小冉：晚风凉凉的，你也要注意别着凉。"]
    ];
    const level0 = [
        [`${player.name}`,"你好，冒昧过来搭话，希望没有打扰你。",`小冉：你好，请问找${player.name}有事吗？`],
        [`${player.name}`,"我只是路过，看你独自站在这里。",`小冉：我们好像不怎么熟悉，${player.name}为什么来找我？`],
        [`${player.name}`,"别紧张，我没有恶意。","小冉：我不太习惯和陌生人聊天，请多多包涵。"]
    ];
    const level1 = [
        [`${player.name}`,"很高兴能在这里遇见你。",`小冉：很高兴能和${player.name}聊聊天。`],
        [`${player.name}`,"我愿意静下心听你说说心里话。","小冉：平时很少有人愿意停下来听我说话。"],
        [`${player.name}`,"能认识你，我也觉得很幸运。",`小冉：能认识${player.name}，我觉得挺幸运的。`]
    ];
    const level2 = [
        [`${player.name}`,"最近有没有遇到什么有趣的小事？",`小冉：最近发生了很多小事，想讲给${player.name}听。`],
        [`${player.name}`,"和你聊天的时候，感觉时间过得很快。",`小冉：和${player.name}聊天的时候，时间过得好快。`],
        [`${player.name}`,"每次过来见到你，我都会很开心。",`小冉：每次见到${player.name}，心情都会变好。`]
    ];
    const level3 = [
        [`${player.name}`,"如果心里有心事，可以放心跟我说。",`小冉：我有很多藏在心里的话，只愿意告诉${player.name}。`],
        [`${player.name}`,"不管开心或者难过，我都愿意陪着你。","小冉：不管开心还是难过，我第一个想到的就是你。"],
        [`${player.name}`,"我很珍惜我们之间的这份情谊。","小冉：我很珍惜我们现在的关系。"]
    ];
    const level4 = [
        [`${player.name}`,"只要和你聊天，我就觉得很安心幸福。",`小冉：只要和${player.name}聊天，我就觉得无比幸福❤️`],
        [`${player.name}`,"你的所有心事，我都会好好珍藏。","小冉：我的心事，全部都愿意向你敞开。"],
        [`${player.name}`,"你对我而言，是特别重要的人。",`小冉：${player.name}，你是我生命里最重要的人。`]
    ];
    const all_level = [level0,level1,level2,level3,level4];
    let pick;
    if(player.time==="白天") pick = day_dialog[randInt(0,day_dialog.length-1)];
    else pick = night_dialog[randInt(0,night_dialog.length-1)];
    let pick_lv = all_level[level][randInt(0,all_level[level].length-1)];
    const [p_spk,p_msg,x_msg] = pick;
    const [plv_spk,plv_msg,xlv_msg] = pick_lv;
    print(`${p_spk}：${p_msg}`);await sleep(700);
    print(x_msg);await sleep(700);
    print(`${plv_spk}：${plv_msg}`);await sleep(700);
    print(xlv_msg);
    print(`聊天结束！信任 +${add}`);
    setTimeout(()=>showMainMenu(),1200);
}

//主菜单
function showMainMenu(){
    clearLog();clearBtn();
    renderStatus();
    print("\n========== 小冉治愈物语 ==========");
    btnArea.innerHTML += `<button onclick="chatWithXiaoran()">1.和小冉聊天</button>`;
    btnArea.innerHTML += `<button onclick="sweetInteract()">2.亲密陪伴</button>`;
    btnArea.innerHTML += `<button onclick="travelWithXiaoran()">3.外出双人旅行</button>`;
    btnArea.innerHTML += `<button onclick="rockPaperScissors()">4.玩猜拳游戏</button>`;
    btnArea.innerHTML += `<button onclick="playGameWithXiaoran()">5.玩猜数字游戏</button>`;
    btnArea.innerHTML += `<button onclick="proposeMarry()">6.求婚</button>`;
    btnArea.innerHTML += `<button onclick="changeTime()">7.切换时间</button>`;
    btnArea.innerHTML += `<button onclick="shop()">8.商店（购买礼物）</button>`;
    btnArea.innerHTML += `<button onclick="giveGift()">9.赠送背包礼物</button>`;
    btnArea.innerHTML += `<button onclick="workJob()">10.打工赚钱</button>`;
    btnArea.innerHTML += `<button onclick="saveGame()">11.存档</button>`;
    btnArea.innerHTML += `<button onclick="gameExit()">0.退出</button>`;
    window.chatWithXiaoran = chatWithXiaoran;
    window.sweetInteract = sweetInteract;
    window.travelWithXiaoran = travelWithXiaoran;
    window.rockPaperScissors = rockPaperScissors;
    window.playGameWithXiaoran = playGameWithXiaoran;
    window.proposeMarry = proposeMarry;
    window.changeTime = changeTime;
    window.shop = shop;
    window.giveGift = giveGift;
    window.workJob = workJob;
    window.saveGame = saveGame;
    window.gameExit = function(){
        print("\n👋游戏结束，刷新页面重新开始");
    }
}

//游戏入口
async function start(){
    const saved = loadGame();
    if(saved){
        clearLog();
        print("检测到存档！");
        clearBtn();
        btnArea.innerHTML +=`<button onclick="loadSaved()">1.读取存档</button>`;
        btnArea.innerHTML +=`<button onclick="newGame()">2.开启新游戏</button>`;
        window.loadSaved = function(){
            player = saved.player;
            bag = saved.bag;
            showMainMenu();
        }
        window.newGame = function(){
            clearBtn();
            inputArea.innerHTML = `<input type="text" id="nameInput" placeholder="输入你的主角名字"> <button onclick="confirmName()">确认</button>`;
            window.confirmName = function(){
                const nm = document.getElementById("nameInput").value.trim();
                const userName = nm?nm:"主角";
                player = initGameData(userName);
                bag = [];
                showMainMenu();
            }
        }
    }else{
        clearLog();
        print("✨开启新故事，请为你自己取一个名字");
        inputArea.innerHTML = `<input type="text" id="nameInput" placeholder="输入你的主角名字"> <button onclick="confirmName()">确认</button>`;
        window.confirmName = function(){
            const nm = document.getElementById("nameInput").value.trim();
            const userName = nm?nm:"主角";
            player = initGameData(userName);
            bag = [];
            showMainMenu();
        }
    }
}
start();
</script>
</body>
</html>
