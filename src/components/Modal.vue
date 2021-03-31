<template>
    <div class="modal-fade">
        <div class="modal">
            <span class="close-button" @click="closeModal"/>
            <div class="modal-content">
                <div class="modal-title">Заявка отправлена.</div>
                <div class="img-area">
                    <img class="modal-img" :src="'/modal-view.jpg'" alt="success">
                </div>
            </div>
        </div>
    </div>
</template>

<script>

export default {
  name: 'Modal',
  methods: {
      closeModal(){
          this.$emit('closeModal')
      }
  }
}
</script>

<style lang="scss">
.modal-fade:before {
	content: '';
	background: #000;
	position: fixed; 
	left: 0;
	top: 0;
	width: 100%; 
	height: 100%;
	opacity: 0.7;
	z-index: 9999;
}
.modal{
    height: 70vh;
    width: 70vw;
    position: fixed;
    top: 15vh;
    left: 15vw;
    background-color: #fff;
    box-shadow: 0 0 20px #000;
    z-index: 99999;
	opacity: 1;
    display: flex;
    flex-direction: column;
}
.modal-content{
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    padding: 10%;
}
.close-button {
    position: absolute;
    right: 20px;
    top: 20px;
    width: 32px;
    height: 32px;
    opacity: 1;
    cursor: pointer;
}
.close-button:hover {
    opacity: 0.3;
}
.close-button:before, .close-button:after {
    position: absolute;
    left: 15px;
    content: ' ';
    height: 33px;
    width: 2px;
    background-color: #333;
}
.close-button:before {
    transform: rotate(45deg);
}
.close-button:after {
    transform: rotate(-45deg);
}
.img-area{
    display: flex;
    justify-content: center;
    width: 100%;
}
.modal-title{
    display: flex;
    justify-content: center;
    font-family: 'Rubik', sans-serif;
    font-size: 1.5em;
}
.modal-img{
    min-width: 190px;
    min-height: 150px;
    width: 500px;
    height: 300px;
}
@media screen and (max-width: 768px){
    .modal-img{
        width: 200px;
        height: 150px;
    }   
    .modal-title{
        font-size: 1.2em;
    }
    .modal{
        height: 40vh;
        width: 70vw;
        top: 30vh;
        left: 15vw;
    }
}
</style>