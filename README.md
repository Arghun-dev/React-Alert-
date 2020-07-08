# React-Alert

To Handle React Alert Based on the MsgId Received from Backend, Just Do this:

### Step 1

First create an Alert component:

DashboardAlert.js

```js
import React from 'react';
import { Alert } from 'react-bootstrap';

// styles
import './DashboardAlert.styles.scss';

class DashboardAlert extends React.Component {
    render() {
        return (
            <Alert
                className='dashboard-alert'
                show={this.props.showAlert}
                variant={this.props.alertStatus}
                onClose={() => this.props.resetDashboardAlert()}
                dismissible
            >
                <Alert.Heading>{this.props.dashboardAlertHeading}</Alert.Heading>
                {this.props.alertText}
            </Alert>
        )
    }
}

export default DashboardAlert;
```
### Step 2

Then import this Alert component into the component which you want to use that:

for example I want to use this Alert component inside my Profile.js component:

**And you have to define several states inside your component to control Alert, Alert Success, Alert Failure and so on**

So I have to initialize a state called for example, formSubmitted inside my state to initialize the logic that when the onClick event called, then show Alert.

Why We Do this ?

**To prevent repeated setState and to prevent componentDidUpdate exceeds setState Error**

**Notice that this is extremely important to do this, you have to do this with onClick function in form**

And also, I have to initialize the initial States of Alert properties: showAlert, alertStatus, dashboardAlertHeading and alertText

Profile.js

```js
import React, { Component } from 'react';
import DashboardAlert from './DashboardAlert';

class Profile extends Component {
  constructor(props) {
    super(props);
    
    this.state = {
      showAlert: false,
      alertStatus: null,
      dashboardAlertHeading: null,
      alertText: null,
      formSubmitted: false
    }
  }
}
```

### Step 3

Then The Main Part of handling Alert, You have to do this inside `componentDidUpdate`.

We Receive `MsgId` and `MsgText` from backend, So we use them to handle React Alert.

Imagine We get `MsgId` and `MsgText` from userPasswordChangeByToken.

userPasswordChangeByToken {
  0: { MsgId: 0 or -1, MsgText: text }
}

```js
import React, { Component } from 'react';
import DashboardAlert from './DashboardAlert';

// REDUX
import { connect } from 'react-redux';

class Profile extends Component {
  constructor(props) {
    super(props);
    
    this.state = {
      showAlert: false,
      alertStatus: null,
      dashboardAlertHeading: null,
      alertText: null,
      formSubmitted: false
    }
  }
  
  componentDidUpdate() {
    if (this.props.userPasswordChangeByTokenMsgId?.[0]) {
      if (this.props.userPasswordChangeByTokenMsgId[0].MsgId === 0 && this.state.formSubmitted === true) {
        this.setState({
          showAlert: true,
          alertText: this.props.userPasswordChangeByTokenMsgId[0].MsgText,
          alertStatus: 'success',
          dashboardAlertHeading: 'عملیات موفق',
          passwordChanged: false
        })

        setTimeout(() => {
          this.setState({
            showAlert: false,
            alertStatus: null,
            dashboardAlertHeading: null,
            alertText: null
          })
        }, 5000)
      }

      if (this.props.userPasswordChangeByTokenMsgId[0].MsgId === -1 && this.state.passwordChanged === true) {
        this.setState({
          showAlert: true,
          alertText: this.props.userPasswordChangeByTokenMsgId[0].MsgText,
          alertStatus: 'danger',
          dashboardAlertHeading: 'عملیات ناموفق',
          passwordChanged: false
        })

        setTimeout(() => {
          this.setState({
            showAlert: false,
            alertStatus: null,
            dashboardAlertHeading: null,
            alertText: null
          })
        }, 5000)
      }
    }
  }
  
  handleSubmit = (e) => {
    e.preventDefault();
    
    // send form
    
    this.setState({
      formSubmitted: true
    })
  }
  
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
         ...
      </form>
    )
  }
}

const mapStateToProps = state => {
  userPasswordChangeByTokenMsgId: state.userPasswordChangeByToken
}

export default connect(mapStateToProps)(Profile);
```

### Last Point

Another very important point that you have to notice:

وقتی دفعه اول یه یوزر اضافه میکردم مثلا یوزر با نام کاربری ارغون به لیت اضافه شد. بعد میرفتم یه یوزر دیگه با نام کاربری سهند اضافه میکردم الرتم باز نشون میداد یوزر با نام کاربری ارغون اضافه شد. پس باید یه کاری میکردم که این اتفاق نیفته. چیکار کردم؟

Always remember to assing prevProps in componentDidUpdate

```js
componentDidUpdate(prevProps) {
    if (this.props.addUserMsg?.[0]?.MsgId === 0 && this.addUserAction && this.state.addUserActionCalled === true) {
            if (prevProps.addUserMsg?.[0]?.MsgText !== this.props.addUserMsg?.[0]?.MsgText) {
                this.setState({
                    showAlert: true,
                    alertStatus: 'success',
                    alertText: this.props.addUserMsg[0].MsgText,
                    dashboardAlertHeading: 'عملیات موفق',
                    addUserActionCalled: false
                })

                setTimeout(() => {
                    this.setState({
                        showAlert: false,
                        alertStatus: null,
                        alertText: null,
                        dashboardAlertHeading: null
                    })
                }, 5000)
            }
        }

        if (this.props.addUserMsg?.[0]?.MsgId === -1 && this.addUserAction && this.state.addUserActionCalled === true) {
            if (prevProps.addUserMsg?.[0]?.MsgText !== this.props.addUserMsg?.[0]?.MsgText) {
                this.setState({
                    showAlert: true,
                    alertStatus: 'danger',
                    alertText: this.props.addUserMsg[0].MsgText,
                    dashboardAlertHeading: 'عملیات ناموفق',
                    addUserActionCalled: false
                })

                setTimeout(() => {
                    this.setState({
                        showAlert: false,
                        alertStatus: null,
                        alertText: null,
                        dashboardAlertHeading: null
                    })
                }, 5000)
            }
        }
  }
```

I had to use prevProps to compare the prevProp to current prop

Hope You enjoyed this!
