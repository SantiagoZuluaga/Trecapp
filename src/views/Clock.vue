<template>
    <div>
        <Nav />
        <b-container>
            <audio id="breathing1">
                <source src="@/assets/audios/breathing1.mp3" type="audio/mp3">
            </audio>
            <audio id="breathing2">
                <source src="@/assets/audios/breathing2.mp3" type="audio/mp3">
            </audio>
            <!--MODALS-->
            <b-modal ref="close-component" hide-header hide-footer>
                <div class="text-center">
                    <h3>Cuidado</h3>
                    <hr />
                    <p>¿Estas seguro de querer salir?</p>
                    <b-button variant="primary" block v-on:click="() => {
                        this.resetTimer();
                        this.$router.push('/dashboard');
                        }">Aceptar</b-button>
                    <b-button variant="danger" block v-on:click="hideModal()">Cancelar</b-button>
                </div>
            </b-modal>

            <!--BUTTON CLOSE-->
            <b-button variant="danger" v-on:click="showModal()" class="close-component">X</b-button>

            <!--Animation-->
            <b-row class="justify-content-center">
                <div id="circle" class="circle-breathing">
                </div>
                <div class="circle">
                </div>
            </b-row> 

            <!--Timer-->
            <div class="container-buttons-timer">
                <b-row class="text-center">
                    <b-col lg="12">
                        <p v-if="min<10 && sec<10" class="timer-text">0{{min}} : 0{{sec}}</p>
                        <p v-if="min<10 && sec>=10" class="timer-text">0{{min}} : {{sec}}</p>
                        <p v-if="min>=10 && sec<10" class="timer-text">{{min}} : 0{{sec}}</p>
                        <p v-if="min>=10 && sec>=10" class="timer-text">{{min}} : {{sec}}</p>
                    </b-col>
                </b-row>
                <b-row v-if="estado === false" class="text-center">
                    <b-col>
                        <b-button v-on:click="plusMin()" variant="primary" class="button-timer">+</b-button>
                        <b-button v-on:click="minusMin()" variant="primary" class="button-timer">-</b-button>
                        <b-button v-on:click="plusSec()" variant="primary" class="button-timer">+</b-button>
                        <b-button v-on:click="minusSec()" variant="primary" class="button-timer">-</b-button>
                    </b-col>
                </b-row>
                <b-row class="justify-content-center">
                    <b-col v-if="estado === false" lg="2">
                        <b-button class="buttons-clock" v-on:click="playTimer()" block variant="primary">Empezar</b-button>
                    </b-col>
                    <b-col v-if="estado === true" lg="2">
                        <b-button class="buttons-clock" v-on:click="stopTimer()" block variant="primary">Detener</b-button>
                    </b-col>
                    <b-col lg="2">
                        <b-button class="buttons-clock" v-on:click="resetTimer()" block variant="danger">Reiniciar</b-button>
                    </b-col>
                </b-row>
            </div>
        </b-container>  
    </div>
</template>

<script>
import Nav from '@/components/Navbar.vue';
var timer;
var timerAnimation;

export default {
    data() {
        return {
            min: 0,
            sec: 0,
            estado: false,
            sizeBall: 60,
            estadoBall: 0,
            marginBall: 210,
            estadoaudio: false,
            upsize: false,
            downsize: false
        }
    },
    components: {
        Nav
    },
    beforeDestroy () {
        this.min = 0;
        this.sec = 0;
        this.estado = false;
        this.sizeBall = 60;
        this.estadoBall = 0;
        this.marginBall = 210;
        clearInterval(timer);
        clearInterval(timerAnimation);
    },
    methods: {
        plusMin(){
            if(this.estado === false){
                if(this.min<60){
                    this.min = this.min + 1;
                }
            }
        },
        minusMin(){
            if(this.estado === false){
                if(this.min>0){
                    this.min = this.min - 1;
                }
            }
        },
        plusSec(){
            if(this.estado === false){
                if(this.sec<60){
                    this.sec = this.sec + 1;
                }
            }
        },
        minusSec(){
            if(this.estado === false){
                if(this.sec>0){
                    this.sec = this.sec - 1;
                }
            }
        },
        resetAll() {
            this.min = 0;
            this.sec = 0;
            this.estado = false;
            this.sizeBall = 60;
            this.estadoBall = 0;
            this.marginBall = 210;
            this.estadoaudio = false;
            this.upsize = false;
            this.downsize = false;
            var circle = document.getElementById('circle');
            circle.style = "width: 60px; height: 60px; top: 210px;";
        },
        playTimer() {

            if(this.estado === false && this.sec > 0 || this.estado === false && this.min > 0){
                this.estado = true; 

                timer = setInterval(()=>{
                        
                    if(this.sec>0){
                        this.sec = this.sec - 1;
                    }
                    else if(this.min>0){
                        this.min = this.min - 1;
                        this.sec = 59;
                    }
                    else{
                        this.resetAll();
                        clearInterval(timer);
                        clearInterval(timerAnimation);
                    }  

                }, 1000);

                timerAnimation = setInterval(()=>{
                     this.changeSize();
                }, 125/2);

            }
        },
        stopTimer() {
            if(this.estado === true){
                this.estado = false;

                if(this.upsize){
                    document.getElementById("breathing1").pause();
                    this.upsize = false;
                }
                else{
                    document.getElementById("breathing2").pause();
                    this.downsize = false;
                }

                clearInterval(timer);
                clearInterval(timerAnimation);
            }
        },
        resetTimer() {
            if(this.estado === true){
                if(this.upsize){
                    document.getElementById("breathing1").pause();
                    this.upsize = false;
                }
                else{
                    document.getElementById("breathing2").pause();
                    this.downsize = false;
                }
                this.resetAll();
                clearInterval(timer);
                clearInterval(timerAnimation);
            }
            else {
                this.resetAll();
            }
        },
        plusSize() {
            this.sizeBall = this.sizeBall + 2;
            this.marginBall = this.marginBall - 1;

            if(this.sizeBall === 200){
                this.estadoBall = 1;
            }

        },
        minusSize() {
            this.sizeBall = this.sizeBall - 2;
            this.marginBall = this.marginBall + 1;

            if(this.sizeBall === 60){
                this.estadoBall = 0;
            }
        },
        changeSize() {

            if(this.estado === true){

                this.estadoaudio = true;
                if(this.estadoBall === 0){

                    if(this.estadoaudio === true && this.upsize === false){
                        document.getElementById("breathing1").play();
                        this.upsize = true;
                        this.downsize = false;
                    }
                    
                    this.plusSize();
                    var circle = document.getElementById('circle');
                    circle.style = "width: " + this.sizeBall + "px; " + "height: " + this.sizeBall + "px;" + "top: " + this.marginBall + "px;" ;
                }
                else {

                    if(this.estadoaudio === true && this.downsize === false){
                        document.getElementById("breathing2").play();
                        this.upsize = false;
                        this.downsize = true;
                    }

                    this.minusSize();
                    var circle = document.getElementById('circle');
                    circle.style = "width: " + this.sizeBall + "px; " + "height: " + this.sizeBall + "px;" + "top: " + this.marginBall + "px;" ;
                }
            }
        },
        showModal() {
            this.$refs['close-component'].show()
        },
        hideModal() {
            this.$refs['close-component'].hide()
        },
    }
}
</script>

<style>
.clock{
    text-align: center;
    margin-top: 10px;
}

.button-timer{
    font-size: 20px;
    color: white;
    margin: 0px 5px 5px 5px;
    padding: 5px 30px 5px 30px;
}

.timer-title{
    font-size: 35px;
}

.timer-text{
    font-size: 50px;
    margin-bottom: 0px;
}

.circle{
    background: #2F89FC;
    border-radius: 100%;
    height: 50px;
    width: 50px;
    position: absolute;
    top: 215px;
}

.circle-breathing{
    background: #1a65ce;
    border-radius: 100%;
    height: 60px;
    width: 60px;
    position: absolute;
    top: 210px;
}

.container-buttons-timer{
    margin-top: 220px;
}

.close-component{
    margin-top: 20px;
    font-size: 15px;
    margin-bottom: 0px;
}

.buttons-clock{
    margin-top: 10px;
}
</style>
