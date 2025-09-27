<script setup>
import { ref, reactive, onMounted } from 'vue'
import api from "@/api/axios"

const { id } = defineProps({ id: String })

const state = reactive({
    formData: {
        loanLimit: 5,
        loanPeriod: 14,
        name: '',
        tel: '',
        birth: '',
        gender: '남자',
        memo: ''
    }
})

const bookList = ref([]);

onMounted(async () => {
    if(id){
        getMemberDetail(id);
    }
})

function matchData(type, d){
    const matching = {
        birth: 'mbr_birth',
        loanLimit: 'mbr_mx_cnt',
        loanPeriod: 'mbr_mx_date',
        name: 'mbr_nm',
        tel: 'mbr_tel',
        memo: 'memo',
        gender: 'mbr_gender',
    }
    if(type === 'get'){
        for(let key in matching){
            state.formData[key] = d[matching[key]];
        }
    }else if(type === 'set'){

    }
}

function onlyTel(e) {
  // 숫자와 하이픈만 허용
  state.formData.tel = e.target.value.replace(/[^0-9-]/g, '')
}


async function getMemberDetail(id){
    const res = await api.get(`/members/${id}`);
    matchData('get', res.data.member);
    bookList.value = res.data.books;
}
async function getBookList(id){
    // const res = await api.get(`/members/${id}`);
    // matchData('get', res.data.member);
    // bookList.value = res.data.books;
}
</script>

<template>
    <main>
        <h2 class="page-title"><span>회원{{ id ? '정보' : '가입' }}</span></h2>
        <article class="item-form">
            <div class="mb-10px">
                <label class="flex align-center">
                    <strong class="title ast">이름</strong>
                    <input type="text" class="input-form" v-model="state.formData.name" />
                </label>
            </div>
            <div class="mb-10px">
                <label class="flex align-center">
                    <strong class="title ast">전화번호</strong>
                    <input type="tel" class="input-form" v-model="state.formData.tel" @input="onlyTel" />
                </label>
            </div>
            <div class="mb-10px">
                <label class="flex align-center">
                    <strong class="title">생년월일</strong>
                    <input type="date" class="input-form" v-model="state.formData.birth" />
                </label>
            </div>
            <div class="flex align-center gender-field mb-10px">
                <strong class="title ast">성별</strong>
                <label>
                    <input type="radio" v-model="state.formData.gender" value="남자" />
                    <strong>남</strong>
                </label>
                <label>
                    <input type="radio" v-model="state.formData.gender" value="여자" />
                    <strong>여</strong>
                </label>
            </div>
            <div class="mb-10px">
                <label class="flex align-center">
                    <strong class="title ast">최대 대여 권수</strong>
                    <input type="number" class="input-form" v-model="state.formData.loanLimit" />
                </label>
            </div>
            <div class="mb-10px">
                <label class="flex align-center">
                    <strong class="title ast">반납 기한</strong>
                    <input type="number" class="input-form" v-model="state.formData.loanPeriod" />
                </label>
            </div>
            <div>
                <textarea placeholder="메모" class="input-form textarea-form"></textarea>
            </div>
            <div>
                <div></div>
            </div>
            <div class="btns">
                <div>
                    <button class="btn-go-list">목록</button>
                </div>
                <div v-if="!id">
                    <button class="btn-green">가입</button>
                </div>
                <div v-else>
                    <button class="btn-green">수정</button>
                    <button class="btn-red">삭제</button>
                </div>
            </div>
        </article>
        <article v-if="bookList.length" class="borrowed-book-field">
            <h3><span>대여 목록</span></h3>
            <div class="list-header">
                <div class="tab-ui">
                    <label>
                        <input type="radio" v-model="state.formData.filter" value="all" @change="getBookList()" />
                        <strong>전체</strong>
                    </label>
                    <label>
                        <input type="radio" v-model="state.formData.filter" value="ing" @change="getBookList()" />
                        <strong>대여중</strong>
                    </label>
                    <label>
                        <input type="radio" v-model="state.formData.filter" value="over" @change="getBookList()" />
                        <strong>연체중</strong>
                    </label>
                </div>
                <div>
                    <div>총 <strong>{{ bookList.length }}</strong>건</div>
                </div>
            </div>
            <div class="list-field">
                <ul>
                    <li>
                        <div class="top">
                            <div class="left">[EM00010439] 이문열 삼국지. 7</div>
                            <div class="right"><strong>[199.5]</strong> 애198ㄴ</div>
                        </div>
                        <div class="middle">
                            <div class="left">애니 폭스 지음 ;장은선 옮김 | 뜨인돌</div>
                            <div class="right"><strong>연체중</strong>(원대로)</div>
                        </div>
                        <div class="bottom">
                            <div class="left"></div>
                            <div class="right">
                                11111
                            </div>
                        </div>
                    </li>
                </ul>
            </div>
        </article>
    </main>
</template>

<style scoped lang="scss">
    .page-title{
        margin: 0 0 10px;
        padding: 0 0 10px;

        span{
            background: linear-gradient(to bottom, transparent 50%, var(--main-green) 0);
        }
    }

    .title{
        width: 120px;
        &.ast:after{
            content: ' *';
            color: #f00;
        }
    }

    .input-form{
        -webkit-appearance: none; /* Safari, Chrome */
        -moz-appearance: none;    /* Firefox */
        appearance: none;         /* 표준 */

        width: 240px;
        height: 40px;
        border: 1px solid #ddd;
        background: #fcfcfc;
        border-radius: 0;
        padding: 5px 10px;
        box-sizing: border-box;
    }
    
    
    .gender-field{
        label{
            margin-right: 10px;
            display: flex;
            align-items: center;
            cursor: pointer;

            &:last-of-type{
                margin-right: 0;
            }
        }
        input[type="radio"]{
            margin-right: 5px;
        }
    }

    .textarea-form{
        resize: none;
        width: 360px;
        height: 100px;
    }

    input[type="number"]::-webkit-inner-spin-button,
    input[type="number"]::-webkit-outer-spin-button {
        -webkit-appearance: none;
        margin: 0;
    }

    /* Firefox */
    input[type="number"] {
        -moz-appearance: textfield;
    }

    .btns{
        display: flex;
        align-items: center;

        div{
            margin-right: 5px;

            &:last-of-type{
                margin-right: 0;
            }
            button{
                width: 80px;
                height: 40px;
                margin-right: 5px;
                border: 0;
                color: #fff;
                border-radius: 0;

                &:last-of-type{
                    margin-right: 0;
                }

                &.btn-go-list{
                    background: navy;
                }

                &.btn-green{
                    background: var(--main-green);
                }
                &.btn-red{
                    background: #f00;
                }
            }
        }

    }

    .borrowed-book-field{
        border-top: 1px dashed #888;
        margin-top: 30px;
        padding-top: 30px;

        h3{
            margin: 0 0 10px;
            padding: 0 0 10px;

            span{
                background: linear-gradient(to bottom, transparent 50%, var(--main-green) 0);
            }
        }

        .list-header{
            display: flex;
            align-items: flex-end;
            justify-content: space-between;

            .tab-ui{
                display: flex;
                align-items: center;
                flex-wrap: wrap;
                
                label{
                    margin-right: 5px;
    
                    &:last-of-type{
                        margin-right: 0;
                    }
    
                    input{
                        display: none;
                    }
                    
                    strong{
                        cursor: pointer;
                        display: inline-block;
                        vertical-align: top;
                        width: 80px;
                        line-height: 30px;
                        text-align: center;
                        
                        background: #ccc;
                        color: #999;
                    }
                    
                    input:checked + strong{
                        background: var(--main-green);
                        color: #fff;
                        box-shadow: 0 1px 4px var(--main-green);
                    }
                }
    
    
            }
        }

        .list-field{

            ul{
                list-style: none;
                margin: 0;
                padding: 0;
                padding: 10px;

                li{
                    border-bottom: 1px solid #ddd;
                    
                    &:hover{
                        background: linear-gradient(to right, transparent, var(--main-green), transparent);
                        border-bottom: 1px solid var(--main-green);
                    }

                    & > div{
                        display: flex;
                        align-items: center;
                        justify-content: space-between;
                    }
                }
            }
        }
    }

</style>
