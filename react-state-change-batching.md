React batches state changes, so it doesn't necessarily re-render
on every state change. This is important to remmeber when setting
things like countdowns, where the better condition would be 

      stop when:
        countdown =< 0 

      instead of:
      stop when:
        countdown === 0
