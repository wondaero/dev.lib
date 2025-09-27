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

function sts2kor(sts){
    const matchTable = {
        over: '연체중',
        ing: '대여중',
        overReturn: '반납(연체)',
        return: '반납'
    }

    return matchTable[sts];

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
                        <input type="radio" v-model="state.formData.filter" value="all" @change="getBookList()" />
                        <strong>최근 한달</strong>
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
            </div>
            <div class="list-field">
                <div class="list-top">
                    <div>총 <strong>{{ bookList.length }}</strong>건</div>
                    <div class="list-btns">
                        <button class="btn-excel" @click="toggleModal()">엑셀 다운로드</button>
                    </div>
                </div>
                <ul>
                    <li v-for="item in bookList" :key="item.book_cde">
                        <div class="top">
                            <div class="left"><strong>[{{ item.book_code }}]</strong> {{ item.title }}</div>
                            <div class="right"><strong>[{{ item.class_no }}]</strong> {{item.author_code}}</div>
                        </div>
                        <div class="middle">
                            <div class="left">{{ item.author_info }} | {{ item.pub_info }}</div>
                            <div class="right"><strong :class="item.status">{{ sts2kor(item.status) }}</strong></div>
                        </div>
                        <div class="bottom">
                            <div class="left"></div>
                            <div class="right">
                                <strong>{{ item.loan_dt.split('.')[0].replace('T', ' ') }}</strong>
                                ~
                                <strong :class="item.status">{{ (item.return_dt || item.due_dt2 || item.due_dt).split('.')[0].replace('T', ' ') }}</strong>
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
        border-top: 1px solid #eee;
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
            margin-bottom: 10px;

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
            border: 1px solid #ddd;
            border-radius: 8px;
            padding: 10px;
            box-shadow: 0 2px 8px rgba(0, 0, 0, .2);
            // margin-bottom: 10px;

            .list-top{
                padding-bottom: 10px;
                border-bottom: 1px dashed #888;

                display: flex;
                align-items: flex-end;
                justify-content: space-between;

                .list-btns{
                    display: flex;
                    align-items: center;

                    .btn-excel{
                        background: #0f713c;
                        color: #fff;
                        height: 40px;
                    }
                }
            }

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

                        &.top,
                        &.middle{
                            .left{
                                overflow: hidden;
                                text-overflow: ellipsis;
                                white-space: nowrap;
                                flex: 1;
                            }
                        }

                        &.middle{
                            .right{
                                strong{
                                    padding: 0 5px;
                                    color: #fff;
                                }
                                .ing{
                                    background: #000;
                                }
                                .over{
                                    background: #f00;
                                }
                                .overReturn{
                                    background: orange;
                                    // color: #000;
                                }
                                .return{
                                    background: green;
                                }
                            }
                        }
                        &.bottom{
                            .right{
                                .ing{
                                    color: #000;
                                }
                                .over{
                                    color: #f00;
                                }
                                .overReturn{
                                    color: orange;
                                    // color: #000;
                                }
                                .return{
                                    color: green;
                                }
                            }
                        }
                    }
                }
            }
        }
    }

</style>
