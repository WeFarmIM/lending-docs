# Modifiers

### Overview

We describe all the modifiers used in our contracts here.

### onlySupportedToken(token)

The token need to be registered in TokenRegistry, otherwise it will throw "Unsupported token" error.

### onlyEnabledToken(token)

The token need to be enabled in TokenRegistry, otherwise it will throw "The token is not enabled" error. One token can be registered but disabled later.

### nonReentrant

We are using this modifier provided by InitializableReentrancyGuard contract of OpenZeppelin to avoid reentrancy attack.

### whenNotPaused

We added a flag variable called `_paused` to our contract, and if we set that variable to true the methods with this modifier can't be called. This is used to temporarily pause some functions in our contracts for emergency issues.
