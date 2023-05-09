=========================================
SmartThings Customized libvmod-parseform
=========================================

See `UPSTREAM.rst <UPSTREAM.rst>`_ for the original upstream README.

Some SmartThings services depend on the `libvmod-parseform <https://github.com/xcir/libvmod-parseform/>`_ library.  We maintain SmartThings-specific changes to the library, not all of which have necessarily been integrated into the upstream repository.

--------------------
Versioning Standard
--------------------

This repo is tagged as ``v<upstream>-st<release>``, where ``<upstream>`` is the most recent upstream tag and ``<release>`` is the sequentially-numbered SmartThings release.  So, ``v62.6-st1`` is the first SmartThings release after upstream ``v62.6``.

--------
History
--------

When authvarnish was originally being developed in 2020, there was a problem with varnish repeatedly crashing due to a segmentation fault.  The root cause was traced back to a problem with the way certain headers were being handled - Akka apparently does something a little unusual with these headers, which triggered the behavior.

The original fix was in commit `f74e824 <https://github.com/PhysicalGraph/libvmod-parseform/commit/f74e824c7b46a143a0155e43103cb89d9fecdee9>`_ on the ``varnish61`` branch in this fork.  This was fix eventually merged into the upstream ``varnish61`` branch in commit `04843de <https://github.com/xcir/libvmod-parseform/commit/04843de5c51b397ae439d1bac5e2ed825db3ca94>`_.

The upstream maintainers never integrated this fix into their own ``master`` branch.  This became a problem when we migrated authvarnish to Ubuntu focal in early 2023, since focal includes Varnish v6.2, and Debian packages for older versions of varnish can no longer be installed.  To address this, we cherry-picked the original changes into this fork's ``master`` branch in commit `3ab0c0e <https://github.com/PhysicalGraph/libvmod-parseform/commit/3ab0c0e6abb5084deb33ed3332df9c78fa227b4f>`_.

The key take-away from this fix is that certain headers should be checked with ``strncasecmp`` rather than ``strcasecmp``.  Normally the best practice in C code is to always limit your string checks to a certain length, to avoid accidentally running off the end of a string.

It is likely that we will need to maintain this patch when upgrading to newer versions of Ubuntu.  As of this writing, Ubuntu jammy has varnish 6.6, and it's not clear that libvmod-parseform even supports that version of varnish.  Upgrading past Ubuntu focal may require a larger effort, and we may need to maintain our own version of the library indefintely.

