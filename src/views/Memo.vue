<template>
    <form id="memoForm" @submit.prevent="modifyMemo">
        <textarea
            :rows="10"
            v-model="fix_memo"
            class="form-control inputField-customizable"
        />
        <textarea
            :rows="10"
            v-model="var_memo"
            class="form-control inputField-customizable"
        />
        <a-button
            variant="success"
            type="primary"
            click="modifyMemo"
            htmlType="submit"
            form="memoForm"
            value="Submit"
            block
        >
            提交
        </a-button>
    </form>
</template>
<script>
import {Auth} from '@aws-amplify/auth';
import {message} from 'ant-design-vue';

export default {
    name: 'Memo',
    components: {
        // MailTwoTone,
    },
    data() {
        return {
            fix_memo: '',
            var_memo: '',
            loading: false
        };
    },
    mounted: async function() {
        // const s3 = new AWS.S3();
        const AWS = require('aws-sdk');

        AWS.config.region = 'ap-southeast-2';
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
                        .then((res) => (this.fix_memo = res.Body.toString()))
                        .catch((err) =>
                            console.error('s3 file get error', err)
                        );
                    params = {Bucket: 'mypollytalk', Key: 'varMemo.txt'};
                    s3.getObject(params)
                        .promise()
                        .then((res) => (this.var_memo = res.Body.toString()))
                        .catch((err) =>
                            console.error('s3 file get error', err)
                        );
                }
            });
        });
    },
    methods: {
        async modifyMemo() {
            const AWS = require('aws-sdk');
            const s3 = new AWS.S3();
            const promises = [];
            const paramsFixMemo = {
                Bucket: 'mypollytalk',
                Key: 'fixMemo.txt',
                Body: this.fix_memo
            };
            promises.push(s3.putObject(paramsFixMemo).promise());
            const paramsVarMemo = {
                Bucket: 'mypollytalk',
                Key: 'varMemo.txt',
                Body: this.var_memo
            };
            promises.push(s3.putObject(paramsVarMemo).promise());
            this.loading = true;
            Promise.all(promises)
                .then((res) => {
                    console.log('write memo res:', res);
                    const lambda = new AWS.Lambda();
                    var params = {
                        FunctionName: 'aicontrolMemoMp3Gen'
                    };
                    lambda.invokeAsync(params, function(err ) {
                        if (err) console.log(err, err.stack);
                        // an error occurred
                        else
                            message.info('提交成功！').then(() => {
                                this.loading = false;
                            }); // successful response
                    });
                })
                .catch((err) => message.warning('出错啦：' + err));
        }
    }
};
</script>
