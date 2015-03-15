**NOTE: the contents on this page is incorrect**


# Details #

Regardless of whether the _--with-delete-operation_ flag is given it always shows that its disabled during a configure. The **AC\_ARG\_ENABLE** block didn't have a variable set for checking.

old block:
```
# Check for delete operation
AC_ARG_ENABLE(delete_operation, AS_HELP_STRING([--with-delete-operation   enable the delete operation]) )

if test x"$with_delete_operation" == "xyes" ; then

    echo "delete operation is enabled"

    AC_DEFINE(DELETE_OPERATION, 1, [enable the delete operation])

else

    echo "delete operation is disabled"

fi
```

new block sets the variable _with\_delete\_operation_ to "yes" so that it can be checked by the bash _if_ statement:
```
AC_ARG_ENABLE(delete-operation, [--with-delete-operation   enable the delete operation],[with_delete_operation=yes
])

if test "$with_delete_operation" == "yes" ; then
	echo "delete operation is enabled"
	AC_DEFINE(DELETE_OPERATION, 1, [enable the delete operation])
else
	echo "delete operation is disabled"
fi
```


# Status #