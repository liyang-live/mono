CLASS=$(mcs_topdir)/class/lib/$(DEFAULT_PROFILE)

with_mono_path = MONO_PATH=$(CLASS)

RUNTIME = $(top_builddir)/runtime/mono-wrapper --debug
MCS = $(with_mono_path) $(RUNTIME) $(CSC) -noconfig -nologo -debug:portable -target:library

if INSTALL_AOT_ONLY
prereq: aot
else
prereq: test/asm.dll
endif

test/.dirstamp deps/.dirstamp:
	-mkdir $(@D)
	rm -f $@ && date > $@

test/asm.dll: test/.dirstamp
deps/test.dll deps/TestBase.dll: deps/.dirstamp

test/asm.dll: deps/test.dll asm.cs
	$(MCS) -out:test/asm.dll -r:deps/TestBase.dll -r:deps/test.dll $(srcdir)/asm.cs

deps/test.dll: deps/TestBase.dll Test.cs
	$(MCS) -out:deps/test.dll -r:deps/TestBase.dll $(srcdir)/Test.cs

deps/TestBase.dll: TestBase.cs
	$(MCS) -out:deps/TestBase.dll $(srcdir)/TestBase.cs

EXTRA_DIST = asm.cs Test.cs TestBase.cs

clean:
	rm -f deps/*.dll test/*.dll

.PHONY: aot
aot: test/asm.dll
	MONO_PATH="deps:$(CLASS)" $(RUNTIME) $(AOT_BUILD_FLAGS) deps/test.dll
	MONO_PATH="deps:$(CLASS)" $(RUNTIME) $(AOT_BUILD_FLAGS) deps/TestBase.dll
	MONO_PATH="deps:$(CLASS)" $(RUNTIME) $(AOT_BUILD_FLAGS) test/asm.dll

