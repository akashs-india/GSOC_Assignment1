#
# Locale file makefile for CUPS.
#
# Copyright 2007-2017 by Apple Inc.
# Copyright 1993-2007 by Easy Software Products.
#
# Licensed under Apache License v2.0.  See the file "LICENSE" for more information.
#

include ../Makedefs


OBJS	=	checkpo.o po2strings.o strings2po.o
TARGETS	=	checkpo po2strings strings2po


#
# Make everything...
#

all:	$(TARGETS)


#
# Make library targets...
#

libs:


#
# Make unit tests...
#

unittests:


#
# Clean all config and object files...
#

clean:
	$(RM) $(TARGETS) $(OBJS)


#
# Update dependencies (without system header dependencies...)
#

depend:
	$(CC) -MM $(ALL_CFLAGS) $(OBJS:.o=.c) >Dependencies


#
# Install all targets...
#

install:	all install-data install-headers install-libs install-exec


#
# Install data files...
#

install-data: $(INSTALL_LANGUAGES)

install-languages:
	$(INSTALL_DIR) -m 755 $(LOCALEDIR)
	for loc in $(LANGUAGES) ; do \
		if test -f cups_$$loc.po; then \
			$(INSTALL_DIR) -m 755 $(LOCALEDIR)/$$loc ; \
			$(INSTALL_DATA) cups_$$loc.po $(LOCALEDIR)/$$loc/cups_$$loc.po ; \
		fi ; \
	done

install-langbundle: po2strings
	$(INSTALL_DIR) -m 755 "$(BUILDROOT)$(BUNDLEDIR)/Resources/English.lproj"
	$(INSTALL_DATA) cups.strings "$(BUILDROOT)$(BUNDLEDIR)/Resources/English.lproj"


#
# Install programs...
#

install-exec:


#
# Install headers...
#

install-headers:


#
# Install libraries...
#

install-libs:


#
# Uninstall files...
#

uninstall: $(UNINSTALL_LANGUAGES)

uninstall-languages:
	-for loc in $(LANGUAGES) ; do \
		$(RM) $(LOCALEDIR)/$$loc/cups_$$loc.po ; \
	done

uninstall-langbundle:
	$(RM) "$(BUILDROOT)$(BUNDLEDIR)/Resources/English.lproj/cups.strings"


#
# pot - Creates/updates the cups.pot template file, merges changes into existing
#       message catalogs, and updates the cups.strings file.  We don't use
#       xgettext to update the cups.strings file due to known xgettext bugs.
#

pot:	checkpo po2strings
	echo Updating cups.pot...
	mv cups.pot cups.pot.bck
	touch cups.pot
	cd ..; xgettext -o locale/cups.pot -cTRANSLATORS -s \
		--keyword=_ --no-wrap \
		--copyright-holder="Apple Inc." \
		--package-name="CUPS" --package-version="$(CUPS_VERSION)" \
		--msgid-bugs-address="https://github.com/apple/cups/issues" \
		*/*.c */*.cxx
	(cat cups.header; tail +6 cups.pot; cat cups.footer) > cups.pot.N
	mv cups.pot.N cups.pot
	echo Checking cups.pot...
	./checkpo cups.pot
	for loc in *.po ; do \
		if test $$loc = '*.po'; then \
			break; \
		fi; \
		echo Merging changes into $$loc... ; \
		msgmerge -o $$loc -s -N --no-location $$loc cups.pot ; \
	done
	echo Updating cups.strings...
	./po2strings cups.pot cups.strings


#
# checkpo - A simple utility to check PO files for correct translation
#           strings.  Dependency on static library is deliberate.
#
# checkpo filename.po [... filenameN.po]
#

checkpo:	checkpo.o ../cups/$(LIBCUPSSTATIC)
	echo Linking $@...
	$(LD_CC) $(ARCHFLAGS) $(LDFLAGS) -o checkpo checkpo.o \
		../cups/$(LIBCUPSSTATIC) $(LIBGSSAPI) $(SSLLIBS) $(DNSSDLIBS) \
		$(COMMONLIBS) $(LIBZ)

checkall:	checkpo
	./checkpo *.po *.strings


#
# po2strings - A simple utility which uses iconv to convert GNU gettext
#              message catalogs to macOS .strings files.
#
# po2strings filename.po filename.strings
#

po2strings:	po2strings.o ../cups/$(LIBCUPSSTATIC)
	echo Linking $@...
	$(LD_CC) $(ARCHFLAGS) $(LDFLAGS) -o po2strings po2strings.o \
		../cups/$(LIBCUPSSTATIC) $(LIBGSSAPI) $(SSLLIBS) $(DNSSDLIBS) \
		$(COMMONLIBS) $(LIBZ)


#
# strings2po - A simple utility which uses iconv to convert macOS .strings files
#              to GNU gettext message catalogs.
#
# strings2po filename.strings filename.po
#

strings2po:	strings2po.o
	echo Linking $@...
	$(LD_CC) $(ARCHFLAGS) $(LDFLAGS) -o strings2po strings2po.o


#
# Dependencies...
#

include Dependencies
