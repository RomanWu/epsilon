include apps/shared/Makefile
include apps/home/Makefile
include apps/on_boarding/Makefile
include apps/hardware_test/Makefile
include apps/usb/Makefile
apps =

# All selected apps are included. Each Makefile below is responsible for setting
# the $apps variable (name of the app class) and the $app_headers
# (path to the apps header).
$(foreach i,${EPSILON_APPS},$(eval include apps/$(i)/Makefile))

app_objs += $(addprefix apps/,\
  apps_container.o\
  apps_container_storage.o\
  apps_window.o\
  battery_timer.o\
  battery_view.o\
  constant.o\
  backlight_dimming_timer.o\
  empty_battery_window.o\
  exam_pop_up_controller.o\
  global_preferences.o\
  i18n.o\
  lock_view.o\
  main.o\
  math_toolbox.o\
  shift_alpha_lock_view.o\
  suspend_timer.o\
  title_bar_view.o\
  variable_box_controller.o\
  variable_box_empty_controller.o\
)

snapshots_declaration = $(foreach i,$(apps),$(i)::Snapshot m_snapshot$(subst :,,$(i))Snapshot;)
apps_declaration = $(foreach i,$(apps),$(i) m_$(subst :,,$(i));)
snapshots_construction = $(foreach i,$(apps),,m_snapshot$(subst :,,$(i))Snapshot())
snapshots_list = $(foreach i,$(apps),,&m_snapshot$(subst :,,$(i))Snapshot)
snapshots_count = $(words $(apps))
snapshot_includes = $(foreach i,$(app_headers),-include $(i) )
epsilon_app_names = '$(foreach i,${EPSILON_APPS},"$(i)", )'

apps/apps_container_storage.o apps/main.o: CXXFLAGS += $(snapshot_includes) -DAPPS_CONTAINER_APPS_DECLARATION="$(apps_declaration)" -DAPPS_CONTAINER_SNAPSHOT_DECLARATIONS="$(snapshots_declaration)" -DAPPS_CONTAINER_SNAPSHOT_CONSTRUCTORS="$(snapshots_construction)" -DAPPS_CONTAINER_SNAPSHOT_LIST="$(snapshots_list)" -DAPPS_CONTAINER_SNAPSHOT_COUNT=$(snapshots_count) -DEPSILON_APPS_NAMES=$(epsilon_app_names)


i18n_files += $(addprefix apps/language_,$(addsuffix .universal.i18n, $(EPSILON_I18N)))
i18n_files += $(addprefix apps/,\
  shared.de.i18n\
  shared.en.i18n\
  shared.es.i18n\
  shared.fr.i18n\
  shared.pt.i18n\
  shared.universal.i18n\
  toolbox.de.i18n\
  toolbox.en.i18n\
  toolbox.es.i18n\
  toolbox.fr.i18n\
  toolbox.pt.i18n\
  variables.de.i18n\
  variables.en.i18n\
  variables.es.i18n\
  variables.fr.i18n\
  variables.pt.i18n\
)

apps/i18n.h: apps/i18n.cpp
apps/i18n.cpp: $(i18n_files)
	@echo "I18N    $@"
	$(Q) $(PYTHON) apps/i18n.py --header $(subst .cpp,.h,$@) --implementation $@ --locales $(EPSILON_I18N) --files $^

$(app_objs): apps/i18n.h

products += apps/i18n.h apps/i18n.cpp

app_images += apps/exam_icon.png

# Tracking which source file uses which image is painful. But we need to ensure
# that a .png file has been inlined before building any source file that uses
# said image (because it will expect the ".h" file to be there).
# As a shortcut, we simply say that every app file depends on every image. In
# practice, this forces all the images to be before the app.

app_image_objs := $(app_images:.png=.o)
.SECONDARY: $(app_images:.png=.cpp)
$(app_objs): $(app_image_objs)

epsilon.$(EXE): $(app_objs) $(app_image_objs)

TO_REMOVE := apps/main.o apps/i18n.o
TMP := $(app_objs) $(app_image_objs)
VAR := $(filter-out $(TO_REMOVE), $(TMP))
test.$(EXE): $(VAR)

products += epsilon.$(EXE) $(app_objs) $(call INLINER_PRODUCTS,$(app_images))
