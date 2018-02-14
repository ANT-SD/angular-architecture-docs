# Common Angular Errors and Fixes

## **State Slice undefined**

### Bug

 This is caused when you forget to always return an state in the reducer. Ussually the default case of the switch.

 ### Fix

 Always return a state in the reducer functions. Always add a default case.

 ```typescript
 default:
      return state;
 ```