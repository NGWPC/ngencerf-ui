<template>
  <client-only>
    <div class="h-full min-h-screen ">
      <div class="grid grid-rows-12">
        <div class="row-span-1">
          <div>
            <AppHeader />
          </div>
        </div>
        <div class="grid row-span-10">
          <div class="grid grid-rows-12">
            <div class="row-span-12 flex items-center justify-center h-screen-inner">

              <div id="LoginBox" class="bg-white mx-auto px-12 py-12 rounded-[10px] max-w-[510px] loginBox">

                <div v-if="showDialog === 'login'" class="mx-auto px-8 text-left">
                  <form onsubmit="return false">

                    <h1>Login</h1>

                    <div class="mt-10">
                      <label for="uname" style="font-weight: normal;" class="required-label">Email</label><br>
                      <input id="uname" class="w-[350px]" type="text" v-model="userName" placeholder=" Email"
                        aria-label="Username" autocomplete="email" v-on:keypress="autoSubmit" />
                    </div>
                    <div class="mt-4">
                      <label for="pword" style="font-weight: normal;" class="required-label">Password</label><br>
                      <Password id="pword" type="password" autocomplete="current-password" v-model="userPassword"
                        placeholder=" Password" aria-label="Password" toggleMask :feedback="false"
                        class="block w-[350px]" v-on:keypress="autoSubmit" />
                      <Button tabindex="-1" class="c-blue underline text-xs" v-on:click="openResetDialog">
                        Forgot Password
                      </Button>
                    </div>

                    <Button id="LoginButton" :class="buttonClasses" v-on:click="SubmitLoginForm"
                      aria-label="sign in">Sign In</Button>

                    <div class="signupButton underline text-base mt-2" aria-label="sign up">
                      <Button @click="openCreateDialog" class="c-blue">Create an Account</Button>
                    </div>

                  </form>
                </div>

                <div v-else-if="showDialog === 'requireVerify'">
                  <div class="dialog-overlay" @click.self="closeDialog">
                    <div class="dialog-content">
                      <h1>Verify Email Required</h1>
                      <div class="pt-2">You must verify your email address before using the application.</div>
                      <Button id="ResendVerifyButton" :class="buttonClasses" v-on:click="SubmitResendVerifyForm"
                      aria-label="Resend verification email">Resend verification email</Button>
                      <div class="py-4"><hr/></div>
                      <h2>Change Email and Verify</h2>
                      <div class="pt-2">You can optionally enter an Email address here to have your account 
                        updated to use the new address instead.</div>
                      <form @submit.prevent="SubmitResendVerifyForm">
                        <div class="form-group inputBox">
                          <label for="newEmail" class="required-label">Email</label>
                          <InputText v-model="newEmail" id="newEmail" type="newEmail" required />
                        </div>
                      </form>
                      <div>
                        <Button id="ResendVerifyNewButton" v-on:click="SubmitResendVerifyForm"
                        aria-label="Send Verification" :class="buttonClasses" :disabled="disableButtons">Send Verification</Button>
                      </div>
                      <div class="signupButton underline text-base inline pl-6">
                        <Button @click="closeDialog" :class="cancelClasses">Cancel</Button>
                      </div>
                    </div>
                  </div>
                </div>

                <div v-else-if="showDialog === 'confirmVerify'">
                  <div class="dialog-overlay" @click.self="closeDialog">
                    <div class="dialog-content">
                      <h1>Verifying your email...</h1>

                      <div class="py-4">Please wait while we confirm your email verification link.</div>

                      <h2 class="py-2">If verification fails</h2>
                      
                      <div>
                        <Button :class="buttonClasses" v-on:click="openRequireVerifyDialog">Resend Verification</Button>
                      </div>
                      <div class="py-2">
                        <Button tabindex="-1" class="c-blue underline text-xs" v-on:click="closeDialog">
                          Back to Login
                        </Button>
                      </div>
                    </div>
                  </div>
                </div>

                <div v-else-if="showDialog === 'create'">
                  <div class="dialog-overlay" @click.self="closeDialog">
                    <div class="dialog-content">
                      <h1>Create an Account</h1>
                      <form @submit.prevent="SubmitNewAccountForm">
                        <div class="form-group inputBox">
                          <label for="email" class="required-label">Email</label>
                          <InputText v-model="newEmail" id="email" type="email" required />
                        </div>
                        <div class="form-group inputBox">
                          <label for="first_name" class="required-label">First Name</label>
                          <InputText v-model="newFirstName" id="first_name" type="text" required />
                        </div>
                        <div class="form-group inputBox">
                          <label for="last_name" class="required-label">Last Name</label>
                          <InputText v-model="newLastName" id="last_name" type="text" required />
                        </div>
                        <div class="form-group inputBox">
                          <label for="password" class="required-label">Password</label>
                          <Password v-model="newPassword" id="password" type="password" name="password"
                            autocomplete="current-password" required toggleMask class="block">
                            <template #header>
                              <div class="font-semibold text-xm mb-4">Password</div>
                            </template>
                            <template #footer>
                              <Divider />
                              <ul class="pl-2 ml-2 my-0 leading-normal">
                                <li>Cannot be a commonly used password</li>
                                <li>Must be at least 8 characters long</li>
                                <li>Must contain at least one non-numeric character</li>
                              </ul>
                            </template>
                          </Password>
                        </div>
                        <div class="form-group inputBox">
                          <label for="confirmPassword" class="required-label">Confirm Password</label>
                          <Password v-model="confirmPassword" id="confirmPassword" type="password" :feedback="false"
                            required toggleMask class="block" />
                        </div>
                        <div>
                          <Button type="submit" :class="buttonClasses" :disabled="disableButtons">Create Account</Button>
                        </div>
                        <div class="signupButton underline text-base inline pl-6">
                          <Button @click="closeDialog" :class="cancelClasses">Cancel</Button>
                        </div>
                      </form>
                    </div>
                  </div>
                </div>

                <div v-else-if="showDialog === 'reset'">
                  <div class="dialog-overlay" @click.self="closeDialog">
                    <div class="dialog-content">
                      <h1>Reset Password</h1>
                      <div class="pt-2">Enter the Email address you registered with and your last name, and click "Send Email"
                        to have password reset instructions sent to you.</div>
                      <form @submit.prevent="SubmitResetPasswordForm">
                        <div class="form-group inputBox">
                          <label for="email" class="required-label">Email</label>
                          <InputText v-model="resetEmail" id="reset_mail" type="email" required />
                        </div>
                        <div>
                          <Button type="submit" :class="buttonClasses" :disabled="disableButtons">Send Email</Button>
                        </div>
                        <div class="signupButton underline text-base inline pl-6">
                          <Button @click="closeDialog" :class="cancelClasses">Cancel</Button>
                        </div>
                      </form>
                    </div>
                  </div>
                </div>

                <div v-else-if="showDialog === 'password'">
                  <div class="dialog-overlay" @click.self="closeDialog">
                    <div class="dialog-content">
                      <h1>Set a new password</h1>
                      <form @submit.prevent="SubmitPasswordForm">
                        <div class="form-group inputBox">
                          <label for="password" class="required-label">Password</label>
                          <Password v-model="newPassword" id="new_password" type="password" name="new_password"
                            autocomplete="current-password" required toggleMask class="block">
                            <template #header>
                              <div class="font-semibold text-xm mb-4">Password</div>
                            </template>
                            <template #footer>
                              <Divider />
                              <ul class="pl-2 ml-2 my-0 leading-normal">
                                <li>Cannot be a commonly used password</li>
                                <li>Must be at least 8 characters long</li>
                                <li>Must contain at least one non-numeric character</li>
                              </ul>
                            </template>
                          </Password>
                        </div>
                        <div class="form-group inputBox">
                          <label for="confirmPassword" class="required-label">Confirm Password</label>
                          <Password v-model="confirmPassword" id="new_confirmPassword" type="password" :feedback="false"
                            required toggleMask class="block" />
                        </div>
                        <div>
                          <Button type="submit" :class="buttonClasses" :disabled="disableButtons">Update Password</Button>
                        </div>
                        <div class="signupButton underline text-base inline pl-6">
                          <Button @click="closeDialog" :class="cancelClasses">Cancel</Button>
                        </div>
                      </form>
                    </div>
                  </div>
                </div>

                <div class="required-hint mt-4" v-if="showDialog !== 'confirmVerify'">
                  <span class="required-asterisk">*</span> Required field
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="row-span-1">
        <AppFooter />
      </div>
    </div>
  </client-only>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import { useToast } from "primevue/usetoast";
import InputText from 'primevue/inputtext';
import Password from 'primevue/password';

import type { ToastMessageOptions } from "primevue/toast";
import { ToastTimeout } from "@/composables/NgencerfEnums";

import { useUserDataStore } from "@/stores/common/UserDataStore";
import { generalStore } from "@/stores/common/GeneralStore";

import AppFooter from "@/components/Common/AppFooter.vue";
import AppHeader from "@/components/Common/AppHeader.vue";

import { useBackendConfig } from "@/composables/UseBackendConfig";

const { serverInfo, gitInfo, menuIndex, calibrationTabIndex, evaluationTabIndex, forecastTabIndex, verificationTabIndex } = storeToRefs(generalStore());

const { popupActive } = storeToRefs(generalStore());

const { calibrationJobId } = storeToRefs(generalStore());

const { logUserIn, setUserName, hardResetUserDataStore, isUserLoggedIn, isUserVerified, getAccessToken } = useUserDataStore();
const { resetGeneralStore, clearToastRecords, addToastRecord, getServerInfo, setServerInfo } = generalStore();

const { ngencerfBaseUrl } = useBackendConfig();

const toast = useToast();
const userDataStore = useUserDataStore();
const userName = ref<string>("");
const userPassword = ref<string>("");
const showDialog = ref<string>("login");

const newEmail = ref('');
const newFirstName = ref('');
const newLastName = ref('');
const newPassword = ref('');
const confirmPassword = ref('');

const resetEmail = ref('');
const resetLastName = ref('');

const disableButtons = ref<boolean>(false);
const buttonClasses = ref<string[]>(["ngenButtonDiv", "btn-left", "mt-4"]);
const cancelClasses = ref<string[]>(['c-blue'])

const route = useRoute();

onMounted(() => {
  popupActive.value = false;
  nextTick(async () => {
    if (!isUserLoggedIn()) {
      sessionStorage.clear();
      localStorage.clear();
      hardResetUserDataStore();
    }
    clearToastRecords();
    calibrationJobId.value = 0;
    resetGeneralStore();
    await getFooterInformation();

    if (route.query?.token) {
      // If UID and Token are provided:
      // - If action is "reset", show fields to set new password
      // - Else, an Email verification link was clicked - immediately verify
      if (route.query?.uid && route.query?.action === 'reset-password') {
        openPasswordDialog();
      } else {
        openConfirmVerifyDialog();
        if (route.query?.action === 'verify-email') {
          await $fetch<any>(`${ngencerfBaseUrl}/auth/users/verify_email_confirm/`, {
            method: 'POST',
            body: {
              token: route.query.token
            }
          }).then(response => {
            // if user is verified, go to Login screen
            toast.removeAllGroups();
            const tMsg: ToastMessageOptions = { severity: 'success', summary: 'Email Verified', detail: 'Your Email address has been verified. Enter your username and password to log in.', life: ToastTimeout.timeoutSuccess };
            toast.add(tMsg); addToastRecord(tMsg);
            if (isUserLoggedIn()) {
              GetExternalInfo();
              GoToLanding();
            } else {
              closeDialog();
            }
          }
          ).catch(error => {
            if (error) {
              let err = error.data?.detail;
              if (!err) {
                err = "Cannot reach server. Error code: " + error.statusCode;
              }
              toast.removeAllGroups();
              const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: err, life: ToastTimeout.timeoutError };
              toast.add(tMsg); addToastRecord(tMsg);
              console.error("Error during user verification:", error.message, error.data.detail);
            }
          });
        } else {
          // invalid action - return to login
          closeDialog();
        }
      }
    }
  });
});

// Get footer infongenCERF
const getFooterInformation = () => {
  makeProtectedApiCall<FormulationTabData>(`${ngencerfBaseUrl}/calibration/get_footer/`, {
    method: "POST",
    headers: {
      "Content-Type": 'application/json'
    },
    body: ""
  }).then((result) => {
    serverInfo.value = result._data;
    if (serverInfo.value) {
      setServerInfo(serverInfo.value);
    }
  })
}

// Get footer infongenCERF
const getGitInformation = () => {
  makeProtectedApiCall<FormulationTabData>(`${ngencerfBaseUrl}/calibration/get_git_info/`, {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${getAccessToken()}`,
      "Content-Type": 'application/json'
    },
    body: ""
  }).then((result) => {
    gitInfo.value = result._data.git_info;
  })
}

const openCreateDialog = () => {
  showDialog.value = "create";
};
const openRequireVerifyDialog = () => {
  if (isUserLoggedIn()) {
    showDialog.value = "requireVerify";
  } else {
    closeDialog();
    toast.removeAllGroups();
    const tMsg: ToastMessageOptions = { severity: 'info', summary: 'Login Required', detail: "A Username and Password must be entered before a verification link can be resent to your Email address.", life: ToastTimeout.timeoutInfo };
    toast.add(tMsg); addToastRecord(tMsg);
  }
};
const openConfirmVerifyDialog = () => {
  showDialog.value = "confirmVerify";
};
const openResetDialog = () => {
  showDialog.value = "reset";
};
const openPasswordDialog = () => {
  showDialog.value = "password";
};
const closeDialog = () => {
  showDialog.value = "login";
  changeButtonState(false);
};

const autoSubmit = (e: KeyboardEvent) => {
  if (e.key === "Enter" && (userName.value.trim() !== "" && userPassword.value.trim() !== "")) {
    SubmitLoginForm(e);
  }
}

/** 
 * Submits the login form
 * @param e - event object
 */
const SubmitLoginForm = async (e: Event) => {
  e.preventDefault(); // prevents the page from reloading

  if (userName.value.trim() !== "" && userPassword.value.trim() !== "") {
    // try to create new access and refresh tokens

    await $fetch<any>(`${ngencerfBaseUrl}/auth/jwt/create/`, {
      method: 'POST',
      body: {
        email: userName.value.toLowerCase(),
        password: userPassword.value
      }
    }).then(response => {
      setUserName(userName.value.toLowerCase());
      // store tokens in UserDataStore
      userDataStore.setAccessToken(response.access);
      userDataStore.setRefreshToken(response.refresh);
      // store user name in UserDataStore
      userDataStore.setFirstName(response.first_name);
      userDataStore.setLastName(response.last_name);
      logUserIn();
      // if user is verified, go to Landing page - otherwise, require them to verify
      if (response.email_verified) {
        GetExternalInfo();
        GoToLanding();
      } else {
        openRequireVerifyDialog();
      }
    }
    ).catch(error => {
      if (error) {
        let err = error.data?.detail;
        if (!err) {
          err = "Cannot reach server. Error code: " + error.statusCode;
        }
        toast.removeAllGroups();
        const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: err, life: ToastTimeout.timeoutError };
        toast.add(tMsg); addToastRecord(tMsg);
        console.error("Error during user creation:", error.message, error.data.detail);
      }
    });
  } else if (userName.value.trim() === "" || userPassword.value.trim() === "") {
    toast.removeAllGroups();
    const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: "A Username and Password are required", life: ToastTimeout.timeoutError };
    toast.add(tMsg); addToastRecord(tMsg);
  }
}

const GetExternalInfo = async () => {
  await getGitInformation();
  serverInfo.value = getServerInfo();
}

const changeButtonState = (disabled: boolean=true) => {
  disableButtons.value = disabled;
  if (disabled) {
    if (!buttonClasses.value.includes('disabledButton')) buttonClasses.value.push('disabledButton');
    if (!cancelClasses.value.includes('disabledLink')) cancelClasses.value.push('disabledLink');
  } else {
    buttonClasses.value.splice(buttonClasses.value.indexOf('disabledButton'), 1);
    cancelClasses.value.splice(cancelClasses.value.indexOf('disabledLink'), 1);
  }
}

const SubmitNewAccountForm = async () => {
  if (newPassword.value !== confirmPassword.value) {
    toast.removeAllGroups();
    const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: 'Passwords do not match.', life: ToastTimeout.timeoutError };
    toast.add(tMsg); addToastRecord(tMsg);
    return;
  }

  changeButtonState(true);

  //try to create a new account for user
  await $fetch<any>(`${ngencerfBaseUrl}/auth/users/`, {
    method: 'POST',
    body: {
      email: newEmail.value.toLowerCase(),
      first_name: newFirstName.value,
      last_name: newLastName.value,
      password: newPassword.value,
      re_password: confirmPassword.value
    }
  }).then(response => {
    changeButtonState(false);
    toast.removeAllGroups();
    const tMsg: ToastMessageOptions = { severity: 'success', summary: 'Success', detail: 'An Email has been sent to ' + (newEmail.value ? newEmail.value : userName.value) + ' with a link to verify your account.', life: ToastTimeout.timeoutSuccess };
    toast.add(tMsg); addToastRecord(tMsg);
    closeDialog();
  }).catch(error => {
    changeButtonState(false);
    toast.removeAllGroups();
    if (error.data?.data.email) {
      let detail = error.data?.data.email[0];
      if (detail.indexOf('already exists')) {
        // customize error message since the one we get back from Djoser isn't ideal
        detail = 'A user with this Email address has already registered.'
      }
      const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: detail, life: ToastTimeout.timeoutError };
      toast.add(tMsg); addToastRecord(tMsg);
    }
    if (error.data?.data.first_name) {
      let detail = error.data?.data.first_name[0];
      const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: detail, life: ToastTimeout.timeoutError };
      toast.add(tMsg); addToastRecord(tMsg);
    }
    if (error.data?.data.last_name) {
      let detail = error.data?.data.last_name[0];
      const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: detail, life: ToastTimeout.timeoutError };
      toast.add(tMsg); addToastRecord(tMsg);
    }
    if (error.data?.data.password) {
      error.data?.data.password.forEach((e: any) => {
        const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: e, life: ToastTimeout.timeoutError }
        toast.add(tMsg); addToastRecord(tMsg);
      });
    }
  });
};

const SubmitResendVerifyForm = async () => {
  changeButtonState(true);

  //send email verification request to server
  let requestBody = {};
  if (newEmail.value) {
    requestBody['new_email'] = newEmail.value;
  }
  await $fetch<any>(`${ngencerfBaseUrl}/auth/users/send_verification_email/`, {
    method: 'POST',
    headers: {
      "Authorization": `Bearer ${getAccessToken()}`,
      "Content-Type": 'application/json'
    },
    body: requestBody
  }).then(response => {
    changeButtonState(false);
    toast.removeAllGroups();
    const tMsg: ToastMessageOptions = { severity: 'success', summary: 'Success', detail: 'An Email has been sent to ' + (newEmail.value ? newEmail.value : userName.value) + ' with a link to verify your account.', life: ToastTimeout.timeoutSuccess };
    toast.add(tMsg); addToastRecord(tMsg);
    closeDialog();
  }).catch(error => {
    changeButtonState(false);
    toast.removeAllGroups();
    if (error.data?.validation_errors) {
      let messageBody = '';
      Object.keys(error.data.validation_errors).forEach((error_field: string) => {
        error.data.validation_errors[error_field].forEach((message: string) => {
          messageBody += message + '\n';
        });
      });
      const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: messageBody, life: ToastTimeout.timeoutError };
      toast.add(tMsg); addToastRecord(tMsg);
    } else {
      let err = error.data?.detail;
      if (!err) {
        err = "Cannot reach server. Error code: " + error.statusCode;
      }
      const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: err, life: ToastTimeout.timeoutError };
      toast.add(tMsg); addToastRecord(tMsg);
    }
  });
};

const SubmitResetPasswordForm = async () => {
  changeButtonState(true);

  // send reset password request to server
  await $fetch<any>(`${ngencerfBaseUrl}/auth/users/reset_password/`, {
    method: 'POST',
    body: {
      email: resetEmail.value.toLowerCase()
    }
  }).then(response => {
    changeButtonState(false);
    toast.removeAllGroups();
    const tMsg: ToastMessageOptions = { severity: 'success', summary: 'Success', detail: 'If we found an account associated with this email address, you will receive an email with instructions to reset your password.', life: ToastTimeout.timeoutSuccess };
    toast.add(tMsg); addToastRecord(tMsg);
    closeDialog();
  }).catch(error => {
    changeButtonState(false);
    toast.removeAllGroups();
    if (error.data?.validation_errors) {
      let messageBody = '';
      Object.keys(error.data.validation_errors).forEach((error_field: string) => {
        error.data.validation_errors[error_field].forEach((message: string) => {
          messageBody += message + '\n';
        });
      });
      const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: messageBody, life: ToastTimeout.timeoutError };
      toast.add(tMsg); addToastRecord(tMsg);
    } else {
      let err = error.data?.detail;
      if (!err) {
        err = "Cannot reach server. Error code: " + error.statusCode;
      }
      const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: err, life: ToastTimeout.timeoutError };
      toast.add(tMsg); addToastRecord(tMsg);
    }
  });
};

const SubmitPasswordForm = async () => {
  if (newPassword.value !== confirmPassword.value) {
    toast.removeAllGroups();
    const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: 'Passwords do not match.', life: ToastTimeout.timeoutError };
    toast.add(tMsg); addToastRecord(tMsg);
    return;
  }

  changeButtonState(true);

  // send reset password request to server
  await $fetch<any>(`${ngencerfBaseUrl}/auth/users/reset_password_confirm/`, {
    method: 'POST',
    body: {
      uid: route.query.uid,
      token: route.query.token,
      new_password: newPassword.value,
      re_new_password: confirmPassword.value
    }
  }).then(response => {
    changeButtonState(false);
    toast.removeAllGroups();
    const tMsg: ToastMessageOptions = { severity: 'success', summary: 'Success', detail: 'Your password has been updated.', life: ToastTimeout.timeoutSuccess };
    toast.add(tMsg); addToastRecord(tMsg);
    closeDialog();
  }).catch(error => {
    changeButtonState(false);
    toast.removeAllGroups();
    if (error.data?.validation_errors) {
      let messageBody = '';
      Object.keys(error.data.validation_errors).forEach((error_field: string) => {
        error.data.validation_errors[error_field].forEach((message: string) => {
          messageBody += message + '\n';
        });
      });
      const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: messageBody, life: ToastTimeout.timeoutError };
      toast.add(tMsg); addToastRecord(tMsg);
    } else {
      let err = error.data?.detail;
      if (!err) {
        err = "Cannot reach server. Error code: " + error.statusCode;
      }
      const tMsg: ToastMessageOptions = { severity: 'error', summary: 'Error', detail: err, life: ToastTimeout.timeoutError };
      toast.add(tMsg); addToastRecord(tMsg);
    }
  });
};

/**
 * Navigates to the landing page.
 */
const GoToLanding = () => {
  navigateTo("LandingPage");
};

</script>
<style lang="scss" scoped>
@use "@/assets/styles/global.scss";
@use "@/assets/styles/styles.scss";

.needAccount {
  font-size: 18px;
  font-weight: 600;
  margin-top: 50px;
}

.signupButton {
  border: 0px;
  margin: 20px auto 0 0;
}

.ngenButtonDiv.disabledButton {
  background-color: darkgray;
}

.disabledLink {
  color: darkgray;
}

.c-blue:hover {
  background-color: transparent;
  font-weight: bold;
  border: none;
}
</style>
