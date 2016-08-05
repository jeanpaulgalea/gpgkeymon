# NAME

gpgkeymon - a shell script to keep tabs on a set of gpg keys.

# VERSION

version 1.20160805

# DESCRIPTION

The primary purpose of this program is to monitor a set of keys and report on their expiration status.

It can be configured to mail a weekly/monthly report to a system administrator.

Or it can also be used as a source for other scripts, e.g. to email the key holder directly or even submit checks to a monitoring system.


# CONFIGURATION

A list of keys to be monitored must be explicitly defined.

This can be done by either providing public key files, something that `gpg --import` can work with, or by providing a list of fingerprints where the corresponding key can be downloaded from a key server.

	cd /path/to/where/gpgkeymon/is/located
	mkdir config

	# using public key files
	mkdir -p config/pubkeys
	gpg --export ABCDEF01 >config/pubkeys/spike.asc

	# subfolder as a group
	mkdir -p config/pubkeys/sysadmins
	gpg --expore ABCDEF02 >config/pubkeys/sysadmins/tom.asc
	gpg --expore ABCDEF03 >config/pubkeys/sysadmins/jerry.asc

	# another group
	mkdir -p config/pubkeys/developers
	gpg --export ABCDEF04 >config/pubkeys/developers/tuffy.asc

	# using fingerprints
	touch config/fingerprints
	echo 75DDC3C4A499F1A18CB5F3C8CBF8D6FD518E17E1 >>config/fingerprints
	echo 790BC7277767219C42C86F933B4FE6ACC0B21F32 >>config/fingerprints


# EXAMPLES

	# use a different gpg binary
	gpgkeymon --gpg /mnt/usr/bin/gpg2

	# use a preferred keyserver when fetching keys by fingerprint
	gpgkeymon --keyserver myhost.mydomain.org

	# warn one month before keys expire (rather than the default 3 months)
	gpgkeymon -w 30

	# only interested in keys without expiry date (top 100)
	gpgkeymon -a 0 --no-expiry 100

	# only revoked and expired keys (top 100)
	gpgkeymon -a 0 --revoked 100 --expired 100

	# same but scriptable
	gpgkeymon -a 0 --revoked 100 --expired 100 -s

	# show me everything except keys which are in good state
	gpgkeymon --ok 0

	# don't hide any section but limit each one to top 100 keys
	gpgkeymon -a 100
