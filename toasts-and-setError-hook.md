# Curaleaf FrontEnd Error Handling

1. Adding toasts (non-invasive alerts) in the frontend to let the user know
  what's going on.

        const onSubmit = async (data: IForm) => {
        try {
          await addInviteesToAppointment({
            appointmentId,
            inviteeName: data.name,
            inviteeEmail: data.email,
            inviteeRelation: data.relation,
          });
          methods.reset();
          addToast("Invitation sent", "success");
        } catch (error: any) {
          addToast("Something went wrong. Please try again", "error");
        }
      };

2. SetError hook is great for notifying the user about input errors on
   forms etc.

       const onSubmit = async (data: IForm) => {
          try {
            const isDuplicateEmail = currentAppointment?.additionalInvitees.some(
              (invitee) => invitee.email === data.email,
            );
            if (isDuplicateEmail) {
              methods.setError("email", {
                type: "manual",
                message: "This invitee email has already been added",
              });
              return;
            }
            await addInviteesToAppointment({
              appointmentId,
              inviteeName: data.name,
              inviteeEmail: data.email,
              inviteeRelation: data.relation,
            });
            methods.reset();
            addToast("Invitation sent", "success");
          } catch (error: any) {
            addToast("Something went wrong. Please try again", "error");
          }
        };

This highlights the form field and lets the user know what the error is. 
![Screenshot 2024-03-25 at 15 21 09](https://github.com/everydaycloud/TIL/assets/126498815/794de6c7-627a-405a-811b-137e3889fba3)

