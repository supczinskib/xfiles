.SUFFIXES: .xpm .abgr

# Convert XPM -> C array of 32-bit ARGB words (0xAARRGGBB).
# Uses RGBA raw output + python repack, so it works even if ImageMagick
# lacks a BGRA encoder/delegate.
.xpm.abgr:
	@set -e; \
	tmp="$@.tmp"; \
	if command -v magick >/dev/null 2>&1; then IMG='magick'; \
	elif command -v convert >/dev/null 2>&1; then IMG='convert'; \
	else echo 'brak magick/convert' >&2; exit 1; fi; \
	{ \
		printf 'unsigned long %s[] = {\n' "$<" | sed 's,icons/,,;s,.xpm,,'; \
		$$IMG "$<" RGBA:- | python3 -c 'import sys; b=sys.stdin.buffer.read(); l=len(b);\
(l%4==0) or sys.exit(f"RGBA len {l} not divisible by 4");\
# RGBA bytes -> 0xAARRGGBB\
sys.stdout.write("".join(f"0x{((b[i+3]<<24)|(b[i]<<16)|(b[i+1]<<8)|b[i+2]):08x},\n" for i in range(0,l,4)))'; \
		printf '};\n'; \
	} > "$$tmp"; \
	grep -q '0x' "$$tmp" || { echo "konwersja $< dała pusty wynik" >&2; rm -f "$$tmp"; exit 1; }; \
	mv -f "$$tmp" "$@"
