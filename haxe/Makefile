STEP1_DEPS = Compat.hx types/Types.hx reader/Reader.hx printer/Printer.hx
STEP3_DEPS = $(STEP1_DEPS) env/Env.hx
STEP4_DEPS = $(STEP3_DEPS) core/Core.hx
SOURCES = $(STEP4_DEPS) StepA_mal.hx
SOURCES_LISP = env/Env.hx core/Core.hx StepA_mal.hx

STEPS = step0_repl step1_read_print step2_eval step3_env \
	step4_if_fn_do step5_tco step6_file step7_quote \
	step8_macros step9_try stepA_mal

HAXE_DIST_MODE = neko
dist_neko = mal.n
dist_python = mal.py
dist_cpp = cpp/mal

all: all-neko all-python all-cpp all-js

all-neko: $(foreach x,$(STEPS),$(x).n)

all-python: $(foreach x,$(STEPS),$(x).py)

all-cpp: $(foreach x,$(STEPS),cpp/$(x))

all-js: $(foreach x,$(STEPS),$(x).js)

dist: mal.n mal.py cpp/mal mal.js mal

mal.n: stepA_mal.n
	cp $< $@

mal.py: stepA_mal.py
	cp $< $@

cpp/mal: cpp/stepA_mal
	cp $< $@

mal.js: stepA_mal.js
	cp $< $@


mal: $(dist_$(HAXE_DIST_MODE))
	$(if $(filter cpp,$(HAXE_DIST_MODE)),\
	  cp $< $@;,\
        $(if $(filter neko,$(HAXE_DIST_MODE)),\
	  nekotools boot $<;,\
        $(if $(filter js,$(HAXE_DIST_MODE)),\
	  echo "#!/usr/bin/env node" > $@;\
	  cat $< >> $@;,\
        $(if $(filter python,$(HAXE_DIST_MODE)),\
	  echo "#!/usr/bin/env python3" > $@;\
	  cat $< >> $@;,\
	$(error Invalid HAXE_DIST_MODE: $(HAXE_DIST_MODE))))))
	chmod +x $@


# Neko target (neko)

s%.n: S%.hx
	haxe -main $(patsubst %.hx,%,$<) -neko $@

step1_read_print.n step2_eval.n: $(STEP1_DEPS)
step3_env.n: $(STEP3_DEPS)
step4_if_fn_do.n step5_tco.n step6_file.n step7_quote.n step8_macros.n step9_try.n stepA_mal.n: $(STEP4_DEPS)


# Python 3 target (python)

s%.py: S%.hx
	haxe -main $(patsubst %.hx,%,$<) -python $@

step1_read_print.py step2_eval.py: $(STEP1_DEPS)
step3_env.py: $(STEP3_DEPS)
step4_if_fn_do.py step5_tco.py step6_file.py step7_quote.py step8_macros.py step9_try.py stepA_mal.py: $(STEP4_DEPS)


# C++ target (cpp)

cpp/s%: S%.hx
	haxe -main $(patsubst %.hx,%,$<) -cpp cpp
	cp $(patsubst cpp/s%,cpp/S%,$@) $@

cpp/step1_read_print cpp/step2_eval: $(STEP1_DEPS)
cpp/step3_env: $(STEP3_DEPS)
cpp/step4_if_fn_do cpp/step5_tco cpp/step6_file cpp/step7_quote cpp/step8_macros cpp/step9_try cpp/stepA_mal: $(STEP4_DEPS)


# JavaScript target (js)

s%.js: S%.hx
	haxe -main $(patsubst %.hx,%,$<) -js $@

JS_DEPS = node_readline.js node_modules
step0_repl.js: $(JS_DEPS)
step1_read_print.js step2_eval.js: $(STEP1_DEPS) $(JS_DEPS)
step3_env.js: $(STEP3_DEPS) $(JS_DEPS)
step4_if_fn_do.js step5_tco.js step6_file.js step7_quote.js step8_macros.js step9_try.js stepA_mal.js: $(STEP4_DEPS) $(JS_DEPS)

node_modules:
	npm install

###

clean:
	rm -f mal.n mal.py cpp/mal mal.js mal
	rm -f step*.py step*.js step*.n
	[ -e cpp/ ] && rm -r cpp/ || true

.PHONY: stats tests $(TESTS)

stats: $(SOURCES)
	@wc $^
	@printf "%5s %5s %5s %s\n" `grep -E "^[[:space:]]*//|^[[:space:]]*$$" $^ | wc` "[comments/blanks]"
stats-lisp: $(SOURCES_LISP)
	@wc $^
	@printf "%5s %5s %5s %s\n" `grep -E "^[[:space:]]*//|^[[:space:]]*$$" $^ | wc` "[comments/blanks]"

