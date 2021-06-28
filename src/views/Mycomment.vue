<template>
    <div class="outer">
        <div id="namebar">
            <Nav showBackArrow="true" showText="true" navText="我被回覆的評論" destination="Myhollow" />
        </div>
        <div class="hollow_container">
            <div class="comment_content" v-for="(item,index) in commentInfo" @click="toReadDiary(index)">
                <div class="comment_user_icon"></div>
                <div class="comment_number_info">匿名樹友 #{{ item.send_number }}</div>
                <div class="comment_date_time_info">{{ item.send_date.split('-').join('/') }}  {{ item.send_time }}</div>
                <div class="comment_content_info">{{ item.content }}</div>
                <div class="comment_hug_icon"></div>
                <div class="comment_hug_info">{{ item.hug }}</div>
            </div>
        </div>
    </div>
</template>
<script>
    import Nav from "@/components/Nav.vue";
    export default {
        name: "Mycomment",
        components: {
            Nav,
        },
        data() {
            return {
                commentInfo: '',
                hollowInfo: '',
                Comment_number_Info: [],
            };
        },
        methods: {
           toReadDiary(select_index) {
                if(this.hollowInfo.filter((element) => {
                    if(element.number == this.commentInfo[select_index].receive_number && 
                    element.date == this.commentInfo[select_index].diary_date &&
                    element.comment_notRead == 'y') {
                        return true;
                    }
                }).length > 0) {
                    this.$http
                    .post("/api/textorder", {
                        order: "UPDATE diary SET comment_notRead='n' where user_id=\'" + this.$store.state.userName + "\' AND date=\'"+this.commentInfo[select_index].diary_date+"\'",
                    }).then((res) => {
                        console.log(res.body);
                    })
                }
               this.$store.commit("setReadMode", "MyReceiveComment");
               this.$store.commit("setReadnumber", this.commentInfo[select_index].receive_number);
               this.$store.commit("setReaddate", this.commentInfo[select_index].diary_date);
               this.$router.push({
                    name: 'DiaryOthers',
                })
           }
        },
        created() {
            this.$http.post("/api/GetCommentInfo", {
                number: this.$store.state.number,
                diary_date: '',
                operatemode: 'MyReceiveComment',
            }).then((res) => {
                if(res.body != "get comment fail") {
                    this.commentInfo = res.body.reverse();
                }
            });
            this.$http.post("/api/GetDiaryInfo", {
                userID: this.$store.state.userName,
                operatemode: "public",
            }).then((res) => {
                if(res.body != "fail") {
                    this.hollowInfo = res.body;
                }
            })
        },
    };
</script>
<style scoped>
    .comment_content {
        position: relative;
        width: 375px;
        height: 111px;
        left: 0px;
        background: #FFFFFF;
        box-shadow: 0px 1px 0px #20E2D7, 0px -1px 0px #20E2D7;
    }
    .comment_user_icon {
        position: absolute;
        width: 36px;
        height: 36px;
        left: 25px;
        top: 11.7px;
        background: url('../assets/Tim/carbon_user-avatar-filled.svg') no-repeat;
        background-size: contain;
    }
    .comment_number_info {
        position: absolute;
        width: 114px;
        height: 18px;
        left: 65px;
        top: 16px;
        font-family: Taipei Sans TC Beta;
        font-size: 13px;
        line-height: 18px;
        display: flex;
        align-items: center;
        color: #5C5C5C;
    }
    .comment_date_time_info {
        position: absolute;
        width: 132px;
        height: 20px;
        left: 65px;
        top: 31px;
        font-family: SF Compact Display;
        font-size: 11px;
        line-height: 20px;
        display: flex;
        align-items: center;
        color: #C7C7C7;
    }
    .comment_content_info {
        position: absolute;
        width: 255px;
        height: 20px;
        left: 28px;
        top: 56px;
        font-family: Taipei Sans TC Beta;
        font-size: 15px;
        line-height: 20px;
        display: flex;
        align-items: center;
        color: #5C5C5C;
        overflow: auto;
    }
    .comment_hug_icon {
        position: absolute;
        width: 19px;
        height: 23px;
        left: 287px;
        top: 34px;
        background: url('../assets/Tim/hug.svg') no-repeat;
        background-size: contain;
    }
    .comment_hug_info {
        position: absolute;
        width: 36px;
        height: 18px;
        left: 312px;
        top: 37px;
        font-family: SF Compact Display;
        font-size: 13px;
        line-height: 18px;
        display: flex;
        align-items: center;
        color: #5C5C5C;
    }
    .hollow_container {
        position: relative;
        width: 375px;
        height: auto;
    }
    #namebar {
        position: relative;
        width: 375px;
        height: 50px;
        box-shadow: 2px 2px 15px rgba(118, 156, 145, 0.25);
    }
    .outer {
        width: 375px;
        height: auto;
        overflow: scroll;
    }
</style>