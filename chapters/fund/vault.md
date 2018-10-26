# vault

## Vault.sol

inherits Spoke (link)

Structures
None

Public State Variables

bool public locked


function lockdown() auth
, changes locked to true.

function unlock() auth

, changes locked to false.
,

// TODO: evaluate whether we need this function, or can just deposit as normal
function deposit(address token, uint amount) auth

function withdraw(address token, uint amount) onlyUnlocked auth

document vault factory?
