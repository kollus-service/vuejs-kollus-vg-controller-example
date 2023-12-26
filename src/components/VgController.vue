<script>
export default {
    name: "VgController",
    data: () => {
        return {
            controller: null,
            speed: 1,
            jumpstep: 10,
            volume: 10,
        }
    },
    mounted() {
        this.loadExternalScripts();
        window.onload = this.initVgController;
    }
    , methods: {
        loadExternalScripts() { // vg controller script url
            this.addExternalScript("https://file.kollus.com/vgcontroller/vg-controller-client.latest.min.js");
            this.addExternalScript("https://file.kollus.com/kollusChatting/sdk/KOLLUS_CHATTING_SDK.latest.js");
        },
        addExternalScript(src) {    // vg controller script load
            const script = document.createElement("script");
            script.setAttribute("src", src);
            document.head.appendChild(script);
        },

        /**
         * VgControllerClient를 초기화하고 플레이어 이벤트에 대한 이벤트 리스너를 설정합니다.
         *
         * @returns {void}
         */
        initVgController() {
            console.log("initVgController Start");
            this.controller = Object.freeze(new VgControllerClient({
                target_window: document.querySelector('#kollus-player').contentWindow
            }));    // controller가 vue의 Proxy 객체 랩핑되지 않도록 Object.freeze() 처리

            try {
                this.controller.on('loaded', () => {    // player load event
                    this.addText('loaded');
                });
                this.controller.on('ready', () => {     // player ready event
                    this.addText('ready');
                });
                this.controller.on('play', () => {      // player play event
                    this.addText('play');
                });
                this.controller.on('progress', (percent, position, duration) => {   // player progress event
                    this.addText(`percent : ${percent}`);
                    this.addText(`position : ${position}`);
                    this.addText(`duration : ${duration}`);

                    let progressBar = document.querySelector('#progress-bar');
                    let progressPercent = document.querySelector('#progress-percent');
                    progressBar.value = percent;
                    progressPercent.innerText = `${percent}%(${position})`;
                });
            } catch (e) {
                console.error(e);
            }
        },
        /**
         * 재생 작업을 처리합니다.
         *
         * @return {void}
         */
        play() {
            this.controller.play();
        },
        /**
         * 작업을 일시 중지합니다.
         *
         * @return {void}
         */
        pause() {
            this.controller.pause();
        },
        /**
         * 빨리 감기 작업을 처리합니다.
         *
         * @return {void}
         */
        ff() {
            this.controller.ff();
        },
        /**
         * 되감기 작업을 처리합니다.
         *
         * @return {void}
         */
        rw() {
            this.controller.rw();
        },
        /**
         * 컨트롤러 속도가 0.5 단위 증가합니다.
         * 현재 속도가 이미 2단위 이상인 경우 속도는 증가하지 않습니다.
         *
         * @return {boolean}
         */
        speedUp() {
            if (this.speed >= 2)
                return false;
            this.speed = this.speed + 0.5;
            this.controller.set_speed(this.speed)
        },
        /**
         * 재생 속도를 0.5씩 감소시킵니다.
         *
         * @return {boolean}
         */
        speedDown() {
            if (this.speed <= 0.5)
                return false;
            this.speed = this.speed - 0.5;
            this.controller.set_speed(this.speed)
        },
        /**
         * 현재 진행 위치를 기준으로 컨트롤러의 반복 시작 위치를 설정합니다.
         *
         * @return {void}
         */
        repeatStart() {
            let position = this.controller.get_progress().position;
            this.controller.set_repeat_start(Math.floor(position));
        },
        /**
         * 현재 진행 위치를 기준으로 반복 종료 위치를 설정합니다.
         *
         * @returns {void}
         */
        repeatEnd() {
            let position = this.controller.get_progress().position;
            this.controller.set_repeat_end(Math.floor(position));
        },
        /**
         * 컨트롤러의 반복 속성을 설정 해제합니다.
         *
         * @returns {void}
         */
        repeatUnset() {
            this.controller.unset_repeat();
        },

        /**
         * 컨트롤러의 점프 스텝 값을 설정합니다.
         *
         * @return {void}
         */
        setJumpstep() {
            this.controller.set_jumpstep(this.jumpstep);
        },

        /**
         * 컨트롤러의 점프 스텝 값을 현재 개체의 점프 스텝 속성에 할당합니다.
         *
         * @return {void}
         */
        getJumpstep() {
            this.jumpstep = this.controller.get_jumpstep();
        },

        /**
         * 컨트롤러의 볼륨을 설정합니다.
         *
         * @return {void}
         */
        setVolume() {
            this.controller.set_volume(this.volume);
        },
        /**
         * 컨트롤러의 볼륨 값을 가져옵니다,
         *
         * @returns {void}
         */
        getVolume() {
            this.volume = this.controller.get_volume();
        },
        /**
         * 오디오를 음소거합니다.
         *
         * @returns {void}
         */
        mute() {
            this.volume = this.controller.mute();
        },
        /**
         * 컨트롤러의 일반 윈도우 모드와 전체화면 모드로 화면 상태를 변경합니다.
         *
         * @returns {void}
         */
        setScreen() {
            this.controller.set_screen();
        },
        /**
         * 컨트롤러에서 현재 화면 상태를 반환합니다
         *
         * @return {string}
         */
        getScreen() {
            alert(this.controller.get_screen());
        },
        /**
         * 화면 확대모드를 contain로 변경합니다.
         *
         * @returns {void}
         */
        contain() {
            this.controller.set_ratio('contain');
        },
        /**
         * 화면 확대모드를 fill로 변경합니다.
         *
         * @returns {void}
         */
        fill() {
            this.controller.set_ratio('fill');
        },
        /**
         * 화면 확대모드를 enlargement로 변경합니다.
         * @return {void}
         */
        enlargement() {
            this.controller.set_ratio('enlargement');
        },
        /**
         * 컨트롤러의 재생 시간을 30으로 설정합니다.
         *
         * @method seek30
         * @return {void}
         */
        seek30() {
            this.controller.set_current_time(30);
        },
        /**
         * 현재 컨트롤러의 북마크를 새로고침 합니다.
         *
         * @return {void}
         */
        refreshBookmark() {
            this.controller.refresh_bookmark();
        },
        /**
         * 컨트롤러에서 전체 화면 버튼을 활성화합니다.
         *
         * @returns {void}
         */
        enableFullscreenButton() {
            this.controller.enable_fullscreen_button();
        },
        /**
         * 컨트롤러에서 플레이어 ID를 반환합니다.
         *
         * @returns {string}
         */
        getPlayerId() {
            alert(this.controller.get_player_id());
        },
        /**
         * 관련 컨트롤러에서 재생 시간을 반환합니다.
         * @method getCurrentTime
         */
        getCurrentTime() {
            alert(this.controller.get_current_time());
        },
        /**
         * 프로그레스를 활성화 하거나 비활성화 합니다
         *
         * @returns {void}
         */
        setProgressOnlyTrue() {
            this.controller.set_controlbar_progress_only(!this.controller.get_controlbar_progress_only());
        },
        /**
         * 컨트롤를 활성화 하거나 비활성화 합니다
         *
         * @returns {void}
         */
        setControlVisibility() {
            this.controller.set_control_visibility(!this.controller.get_control_visibility());
        },
        /**
         * event 요소에 지정된 텍스트를 추가합니다.
         *
         * @param {string} text - The text to be added.
         * @returns {void}
         */
        addText(text) {
            let logText = document.getElementById('event');
            logText.value += '\n';
            logText.value += text;
            logText.scrollTo(0, 99999);
        }
    }
}
</script>

<template>
    <h1>Controls</h1>
    <table>
        <colgroup>
            <col width="30%"/>
            <col width="30%"/>
            <col width="30%"/>
        </colgroup>
        <tr>
            <td>Event</td>
            <td colspan="2"></td>
        </tr>
        <tr>
            <td>
                <textarea id="event" style="width: 70%; height: 100px;"></textarea>
            </td>
            <td colspan="2">
                <div class="progress-container">
                    <progress id="progress-bar" class="progress-bar" value="0" max="100"> 0%</progress>
                    <span id="progress-percent" class="progress-percent">0%</span>
                </div>
            </td>
        </tr>
        <tr>
            <td>Playback</td>
            <td colspan="2"></td>
        </tr>
        <tr>
            <td>
                <div>
                    <button type="button" id="rw" @click="rw">◀◀</button>
                    <button type="button" id="play" @click="play">▶</button>
                    <button type="button" id="pause" @click="pause">⏸</button>
                    <button type="button" id="ff" @click="ff">▶▶</button>
                </div>
            </td>
            <td>
                <div>
                    <button type="button" id="speed_up" @click="speedUp">▲</button>
                    <input type="text" id="speed" readonly="" v-model="speed">
                    <button type="button" id="speed_down" @click="speedDown">▼</button>
                </div>
            </td>
            <td>
                <div>
                    <button type="button" id="repeatStart" @click="repeatStart">▶</button>
                    <button type="button" id="repeatUnset" @click="repeatUnset"> ■</button>
                    <button type="button" id="repeatEnd" @click="repeatEnd">◀</button>
                </div>
            </td>
        </tr>
        <tr>
            <td>Jump Step</td>
            <td colspan="2"></td>
        </tr>
        <tr>
            <td colspan="3">
                <input type="text" id="jumpstep" placeholder="Jumpstep ex> 10, 20, 100..." v-model="jumpstep">
                <button id="set_jumpstep" @click="setJumpstep">Set jumpstep</button>
                <button id="get_jumpstep" @click="getJumpstep">Get jumpstep</button>
            </td>
        </tr>
        <tr>
            <td>Volume</td>
            <td colspan="2"></td>
        </tr>
        <tr>
            <td colspan="3">
                <input type="text" id="volume" placeholder="Volume ex> 0 <= volume <= 100" v-model="volume">
                <button id="set_volume" @click="setVolume">Set volume</button>
                <button id="get_volume" @click="getVolume">Get volume</button>
                <button id="mute" @click="mute">Mute</button>
            </td>
        </tr>
        <tr>
            <td>Screen (exclusive player)</td>
            <td colspan="2"></td>
        </tr>
        <tr>
            <td colspan="3">
                <button id="mute" @click="setScreen">Set Screensize</button>
                <button id="mute" @click="getScreen">Get screen</button>
            </td>
        </tr>

        <tr>
            <td>Etc</td>
            <td colspan="2"></td>
        </tr>
        <tr>
            <td colspan="3">
                <div>
                    <button type="button" id="seek" title="Seek to 30s." @click="seek30">Seek to 30s</button>
                    <button type="button" id="refresh_bookmark" @click="refreshBookmark">Refresh bookmark</button>
                    <button type="button" id="enable_fullscreen_button" @click="enableFullscreenButton">Enable fullscreen button</button>
                    <button type="button" id="get_player_id" @click="getPlayerId">Get player id</button>
                    <button type="button" id="get_current_time" @click="getCurrentTime">Get Current Play Time</button>
                    <button type="button" id="set_progress_only_true" @click="setProgressOnlyTrue">progress only true</button>
                    <button type="button" id="set_ratio_enlargement" @click="setControlVisibility">control visibility</button>
                    <button type="button" id="set_ratio_contain" @click="contain">set_ratio(contain)</button>
                    <button type="button" id="set_ratio_fill" @click="fill">ratio(fill)</button>
                    <button type="button" id="set_ratio_enlargement" @click="enlargement">ratio(enlargement)</button>
                </div>
            </td>
        </tr>
    </table>
</template>

<style scoped>

input {
    width: 30%;
    padding: 18px 18px;
    margin: 8px 0;
    box-sizing: border-box;
    border: none;
    background-color: #f8f8f8;
    font-size: 16px;
}

input:focus {
    outline: none;
    border-bottom: 2px solid #808080;
}

button {
    background-color: #808080;
    border: none;
    color: white;
    display: inline-block;
    padding: 12px 13px;
    margin: 4px 2px;
    text-align: center;
    text-decoration: none;
    font-size: 16px;
    cursor: pointer;
    transition: background-color 0.3s;
    height: 46px;
    vertical-align: middle;
}

button:hover {
    background-color: #606060;
}

progress {
    appearance: none;
    -webkit-appearance: none;
    width: 100%;
    height: 25px;
    border: none;
    border-radius: 8px;
    color: #6bafa1;
    background-color: #727272;
}

.progress-container {
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;
    width: 500px;
    height: 60px;
}

.progress-bar {
    width: 100%;
    height: 100%;
}

.progress-percent {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    text-align: center;

    display: flex;
    align-items: center;
    justify-content: center;
    color: black;
}

table {
    width: 100%;
}

tr, td {
    padding: 10px;
}

</style>
