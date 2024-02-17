<template>
    <div class="login_form_wrapper">
			<div class="container">
				<div class="row">
					<div class="col-md-6 col-md-offset-2">
						<!-- login_wrapper -->
						<div class="login_wrapper">
                            <div class="formsix-pos">
								<div class="form-group i-email">
									<input type="email" class="form-control" required="" id="email2" placeholder="Email Address *">
								</div>
							</div>
							<div class="formsix-e">
								<div class="form-group i-password">
									<input type="password" class="form-control" required="" id="password2" placeholder="Password *">
								</div>
							</div>
							<div class="login_remember_box">
								<label class="control control--checkbox">Remember me
									<input type="checkbox">
									<span class="control__indicator"></span>
								</label>
								<a href="#" class="forget_password">
									Forgot Password
								</a>
							</div>
							<div class="login_btn_wrapper">
								<a href="#" class="btn btn-primary login_btn" disabled> Login </a>
							</div>
                            <h2>or</h2>
                            <div class="row">
                                
                                <div class="col-lg-6 col-md-6 col-xs-12 col-sm-6">
                                    <!-- <GoogleLogin :callback="callback" /> -->
                                    <a href="#" class="btn btn-primary google-plus" @click="login">
                                        Login with Google <i class="fa fa-google-plus"></i>
                                    </a>
								</div>
                                <div class="col-lg-6 col-md-6 col-xs-12 col-sm-6">
                                    <a href="#" class="btn btn-primary facebook"> <span>Login with Facebook</span> <i class="fa fa-facebook"></i> </a>
                                </div>
							</div>
						</div>
						<!-- /.login_wrapper-->
					</div>
				</div>
			</div>
		</div>
</template>


<script>
import { googleSdkLoaded } from "vue3-google-login";
import axios from "axios";

export default {
    name: "SignUpPage",
    data() {
        return {
            userDetails: null
        };
    },
    methods: {
        login() {
            googleSdkLoaded(google => {
                google.accounts.oauth2
                .initCodeClient({
                    client_id:
                    "468120775725-2mtlcreemn2orfddifvg91b1ln8c8rnr.apps.googleusercontent.com",
                    scope: "email profile openid",
                    redirect_uri: "http://localhost:3000/auth/callback",
                    callback: response => {
                    if (response.code) {
                        // console.log('response.code', response.code);
                        this.sendCodeToBackend(response.code);
                    }
                    }
                })
                .requestCode();
            });
        },
        async sendCodeToBackend(code) {
            try {
                const payload = {
                    code,
                    client_id:
                    "468120775725-2mtlcreemn2orfddifvg91b1ln8c8rnr.apps.googleusercontent.com",
                    client_secret: "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",
                    redirect_uri: "postmessage",
                    grant_type: "authorization_code"
                }

                const response = await axios.post("https://oauth2.googleapis.com/token", payload);

                const accessToken = response.data.access_token;

                // Fetch user details using the access token
                const userResponse = await axios.get("https://www.googleapis.com/oauth2/v3/userinfo", {
                        headers: { Authorization: `Bearer ${accessToken}` }
                    }
                );

               if (userResponse && userResponse.data) {
                    // Set the userDetails data property to the userResponse object
                    this.userDetails = userResponse.data;
                    console.log('this.userDetails', this.userDetails)
                } else {
                    // Handle the case where userResponse or userResponse.data is undefined
                    console.error("Failed to fetch user details.");
                }
            } catch (error) {
                console.error("Token exchange failed:", error.response.data);
            }
        }
    }
}
</script>