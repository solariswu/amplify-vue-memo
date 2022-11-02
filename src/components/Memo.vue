<template><div>hello</div> </template>
<script>
// import {Auth} from '@aws-amplify/auth';
// import {message} from 'ant-design-vue';
let AWS = require('aws-sdk');
// import { MailTwoTone } from "@ant-design/icons";

export default {
    name: 'Memo',
    components: {
        // MailTwoTone,
    },
    data() {
        return {
            fix_memo: '',
            var_memo: ''
        };
    },
    mounted: function() {
        let s3 = new AWS.S3({region: 'ap-southeast-2'});

        s3.getObject({Bucket: 'mypollytalk', Key: 'fixMemo.txt'})
            .then((res) => {
                console.log('s3 data', res.data);
            })
            .catch((err) => console.error('s3 file get error', err));

        // Auth.currentSession()
        //     .then((data) =>
        //         this.transformUserInfo(data.getIdToken().getJwtToken())
        //     )
        //     .catch((err) => {
        //         message.warning('You have not login yet.');
        //         console.log(err);
        //     });

        // const plugin = document.createElement('script');
        // plugin.setAttribute(
        //     'src',
        //     'https://unpkg.com/ionicons@5.5.2/dist/ionicons/ionicons.js'
        // );
        // plugin.async = true;
        // document.head.appendChild(plugin);
    },
    methods: {
        // transformUserInfo(idtoken) {
        //     const tokens = idtoken.split('.');
        //     const tokenObj = JSON.parse(
        //         Buffer.from(tokens[1], 'base64').toString()
        //     );
        //     const currentDate = new Date(tokenObj['exp'] * 1000);

        //     console.log('tokenobj', tokenObj);

        //     if (tokenObj) {
        //         for (
        //             let i = 0;
        //             tokenObj.identities && i < tokenObj.identities.length;
        //             i++
        //         ) {
        //             if (i > 0)
        //                 this.idp_names =
        //                     this.idp_names +
        //                     ', ' +
        //                     tokenObj.identities[i].providerName;
        //             else this.idp_names = tokenObj.identities[i].providerName;
        //         }

        //         (this.username = tokenObj['cognito:username']),
        //             (this.role = tokenObj['cognito:roles']
        //                 ? tokenObj['cognito:roles'].join()
        //                 : '---'),
        //             (this.group = tokenObj['cognito:groups']
        //                 ? tokenObj['cognito:groups'].join()
        //                 : '---'),
        //             (this.email = tokenObj['email']),
        //             (this.exp = currentDate.toLocaleString()),
        //             (this.timezone = currentDate
        //                 .toString()
        //                 .match(/\((.*)\)/)
        //                 .pop()),
        //             (this.gravatar = CryptoJS.MD5(
        //                 (this.email ? this.email : '').trim().toLowerCase()
        //             ).toString());
        //     }
        // },
        // async logout() {
        //     let navigate = this.$router;
        //     Auth.signOut({global: true})
        //         .then(() => {
        //             // console.log("logout:", data);
        //             navigate.push('/login');
        //         })
        //         .catch((err) => {
        //             message.error(err.message);
        //         });
        // }
    }
};
</script>
