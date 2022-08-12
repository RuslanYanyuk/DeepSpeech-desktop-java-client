.PHONY: clean apk-clean

include ../definitions.mk

ARCHS := $(shell grep 'ABI_FILTERS' libdeepspeech/gradle.properties | cut -d'=' -f2 | sed -e 's/;/ /g')
GRADLE ?= ./gradlew

all: apk

clean:
	$(GRADLE) clean

libs-clean:
	rm -fr libdeepspeech/libs/*/libdeepspeech.so

libdeepspeech/libs/%/libdeepspeech.so:
	-mkdir libdeepspeech/libs/$*/
	cp ${TFDIR}/bazel-bin/native_client/libdeepspeech.so libdeepspeech/libs/$*/

apk: clean bindings $(patsubst %,libdeepspeech/libs/%/libdeepspeech.so,$(ARCHS))
	cd ./libdeepspeech && cmake -DANDROID_ABI="$(ARCHS)" . && cmake -DANDROID_ABI="$(ARCHS)" --build . && make && make install
	$(GRADLE) jar

maven-bundle: apk
	$(GRADLE) uploadArchives
	$(GRADLE) zipMavenArtifacts

bindings: clean ds-swig
	$(DS_SWIG_ENV) swig -c++ -java -package org.deepspeech.libdeepspeech -outdir libdeepspeech/src/main/java/org/deepspeech/libdeepspeech/ -o jni/deepspeech_wrap.cpp jni/deepspeech.i
