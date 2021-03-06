all: clean C_PERF HS_PERF HS_PROFILE

clean:
	rm -rf stat bin

stat: stat/program-c.perf stat/program-hs.perf stat/program-hs.prof

bin/program-c: program.c
	mkdir -p bin
	clang -O3 -S -emit-llvm program.c -o stat/program-c.ll
	clang -O3 -S program.c -o stat/program-c.s
	clang -O3 program.c -o bin/program-c


bin/program-hs: hs/Main.hs
	mkdir -p bin stat
	pwd
	cd hs && stack clean && \
		stack build --ghc-options="-O2 -ddump-stg -ddump-cmm -ddump-simpl -dsuppress-all -ddump-llvm -ddump-to-file -fllvm" && cp `stack exec -- whereis program | cut -f 2 -d ' '`  ../bin/program-hs
	cp hs/.stack-work/dist/x86_64-linux/Cabal-2.2.0.1/build/program/program-tmp/Main.dump-cmm stat/
	cp hs/.stack-work/dist/x86_64-linux/Cabal-2.2.0.1/build/program/program-tmp/Main.dump-stg stat/
	cp hs/.stack-work/dist/x86_64-linux/Cabal-2.2.0.1/build/program/program-tmp/Main.dump-llvm stat/
	cp hs/.stack-work/dist/x86_64-linux/Cabal-2.2.0.1/build/program/program-tmp/Main.dump-simpl stat/

bin/program-hs-profile: hs/Main.hs
	mkdir -p bin
	pwd
	cd hs && stack clean && \
		stack build --profile && cp `stack exec -- whereis program | cut -f 2 -d ' '`  ../bin/program-hs-profile


.PHONY := C_PROFILE HS_PERF HS_PROFILE

C_PERF: bin/program-c
	mkdir -p stat
	sudo perf stat bin/program-c 2>&1 | tee stat/program-c.stat


HS_PERF: bin/program-hs
	mkdir -p stat
	sudo perf stat bin/program-hs 2>&1 | tee stat/program-hs.stat


HS_PROFILE: bin/program-hs-profile
	mkdir -p stat
	bin/program-hs-profile +RTS -p -hc 2>&1
	cp program-hs-profile.hp stat/
	cp program-hs-profile.prof stat/
