<template>
    <div>
        <form id="signInForm" @submit.prevent="login">
            <label class="label-customizable">固定提醒</label>
            <div v-for="(memoItem, i) in fix_memo" :key="memoItem">
                <input
                    id="signInFormUsername"
                    name="memo"
                    type="text"
                    v-model="fix_memo[i]"
                    class="form-control inputField-customizable"
                    placeholder=""
                    autocapitalize="none"
                    required
                    :disabled="loading"
                    autofocus="true"
                />
            </div>
        </form>
        <form id="signInForm" @submit.prevent="login">
            <label class="label-customizable">备忘录</label>
            <div v-for="(memoItem, i) in var_memo" :key="memoItem">
                <input
                    id="signInFormUsername"
                    name="memo"
                    type="text"
                    v-model="var_memo[i]"
                    class="form-control inputField-customizable"
                    placeholder=""
                    autocapitalize="none"
                    required
                    :disabled="loading"
                    autofocus="true"
                />
            </div>
        </form>
    </div>
</template>
<script>
import {Auth} from '@aws-amplify/auth';
// import {message} from 'ant-design-vue';
// import { MailTwoTone } from "@ant-design/icons";

export default {
    name: 'Memo',
    components: {
        // MailTwoTone,
    },
    data() {
        return {
            item: '',
            fix_memo: [''],
            var_memo: ['']
        };
    },
    mounted: async function() {
        const AWS = require('aws-sdk');
        AWS.config.region = 'ap-southeast-2';
        // const s3 = new AWS.S3();
        Auth.currentSession().then((data) => {
            AWS.config.credentials = new AWS.CognitoIdentityCredentials({
                IdentityPoolId:
                    'ap-southeast-2:3f9cfd44-82d4-425e-affd-c291b527eedc',
                Logins: {
                    // Change the key below according to the specific region your user pool is in.
                    'cognito-idp.ap-southeast-2.amazonaws.com/ap-southeast-2_IUReGReFf': data
                        .getIdToken()
                        .getJwtToken()
                }
            });

            //refreshes credentials using AWS.CognitoIdentity.getCredentialsForIdentity()
            AWS.config.credentials.refresh((error) => {
                if (error) {
                    console.error(error);
                } else {
                    console.log('OK. Successfully logged into identity pool!');
                    const s3 = new AWS.S3({
                        credentials: AWS.config.credentials
                    });

                    let params = {Bucket: 'mypollytalk', Key: 'fixMemo.txt'};
                    s3.getObject(params)
                        .promise()
                        .then(
                            (res) =>
                                (this.fix_memo = res.Body.toString().split(
                                    '\n'
                                )).filter(memo => memo.length > 0)
                        )
                        .catch((err) =>
                            console.error('s3 file get error', err)
                        );
                    params = {Bucket: 'mypollytalk', Key: 'varMemo.txt'};
                    s3.getObject(params)
                        .promise()
                        .then(
                            (res) =>
                                (this.var_memo = res.Body.toString().split(
                                    '\n'
                                )).filter(memo => memo.length > 0)
                        )
                        .catch((err) =>
                            console.error('s3 file get error', err)
                        );
                }
            });
        });
    },
    methods: {}
};
</script>
