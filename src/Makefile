CXX = g++
CXX_FLAGS = -std=c++17 -Wall -Werror -Wextra
BUILD_DIR = obj
TEST_DIR = ./tests
TEST = ./tests/test
SRC  := $(shell find ./s21_containers ./s21_containersplus -name "s21*.cc" | sed -e 's/\.\///')
OBJS := $(SRC:%.cc=$(BUILD_DIR)/%.o)
TEST_SRC := $(shell find $(TEST_DIR) -name "*.cc" | sed -e 's/\.\///')
TEST_OBJS := $(TEST_SRC:%.c=$(BUILD_TEST_DIR)/%.o)
COV_FLAGS = -fprofile-arcs -ftest-coverage --coverage -O0
TEST_FLAGS =-lgtest -lgcov

all: clean s21_containers.a

s21_containers.a: $(OBJS) ./s21_containers/s21_containers.h ./s21_containersplus/s21_containersplus.h
	@mkdir -p $(BUILD_DIR)
	ar rc s21_containers.a $(OBJS)
	ranlib s21_containers.a

test: clean ./tests/*.cc ./tests/test_containers.h
	$(CXX) $(CXX_FLAGS) ./tests/*.cc ./tests/test_containers.h $(TEST_FLAGS) -o ./tests/test
	./tests/test

$(BUILD_DIR)/%.o: %.cc
	@mkdir -p $(dir $@)
	$(CXX) $(CXX_FLAGS) $(FLAGS) -c $< -o $@

gcov_report: clean
	$(CXX) $(CXX_FLAGS) $(COV_FLAGS) $(TEST_OBJS) $(SRC) $(TEST_FLAGS) -o ./tests/test
	./tests/test
	rm -rf ./tests/*.gcno ./tests/*.gcda
	gcovr -r . -s --html --html-details -o ./coverage_report.html
	rm -rf *.gcno *.gcda *.o
	open ./coverage_report.html

style_check:
	@find . \( -name '*.h' -o -name '*.tpp' -o -name '*.cc' \) -print0 | xargs -0 clang-format -n -style=Google

style_fix:
	@find . \( -name '*.h' -o -name '*.tpp' -o -name '*.cc' \) -print0 | xargs -0 clang-format -i -style=Google

leaks_check: clean test
ifeq ($(OS), Darwin)
	leaks --atExit -- test
else
	valgrind --leak-check=full --show-reachable=yes --show-leak-kinds=all --track-origins=yes --log-file=RESULT_VALGRIND.txt ./tests/test
endif

cppcheck:
	cppcheck . 2> cppcheck.log

clean:
	@rm -rf *.o *.a coverage* report RESULT_VALGRIND.txt ./tests/test ./obj cppcheck.log *.gcda *.gcno