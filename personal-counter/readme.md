aptos init --profile default
apos account fund-with-faucet --account default
aptos move compile --named-addresses publisher=default
aptos move publish --named-addresses publisher=default
aptos move run --function-id 'default::counter::bump'