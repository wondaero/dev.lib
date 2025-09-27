<script setup>
  import { reactive } from 'vue'

  const state = reactive({
    memberList: [],
  })
  
  const emit = defineEmits(['close']);

  function closePopup(hasData){
    if(hasData === 'hasData'){
      emit('close', {aa: 11});
    }else{
      emit('close');
    }
  }
</script>

<template>
  <aside @click.self="closePopup()">
    <div>
      <header>
        <h2>엑셀 다운로드</h2>
        <span @click="closePopup()"></span>
      </header>
      <main>
        <article class="">
          <p>데이터 범위를 지정해주세요.</p>
        </article>
        <article class="btns">
          <button class="btn-form" @click="closePopup(state.memberList.filter(x => x.selected))">현재 데이터</button>
          <button class="btn-form" @click="closePopup(state.memberList.filter(x => x.selected))">전체 데이터</button>
        </article>
      </main>
    </div>
  </aside>
</template>

<style scoped lang="scss">
  aside{
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;

    min-width: 720px;
    padding: 10px;
    background: rgba(0, 0, 0, .8);
    display: flex;
    align-items: center;
    justify-content: center;

    & > div{
      max-width: 480px;
      width: 100%;
      background: #fff;

      header{
        padding: 10px;
        background: #ddd;
        display: flex;
        align-items: center;
        justify-content: space-between;


        h2{
          margin: 0;
          font-size: 18px;
        }

        span{
          width: 30px;
          height: 30px;
          position: relative;
          transform: rotate(45deg);
          cursor: pointer;

          &:before, &:after{
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: #000;            
          }
          &:before{
            width: 100%;
            height: 2px;
          }
          &:after{
            height: 100%;
            width: 2px;

          }
        }
      }

      main{
        padding: 10px;

        .btn-form{
          background: var(--main-green);
          line-height: 40px;
          width: 60px;
          border: 0;
          padding: 0;
          border-radius: 0;
          color: #fff;

          &.btn-renew{
            background: orange;
          }

          &.w-80px{
            width: 80px;
          }
        }


        .btns{
          display: flex;
          justify-content: center;

          button{
            width: 120px;

          }

        }
      }

    }

  }
</style>
