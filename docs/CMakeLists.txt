# Dummy output which is never actually produced. Anything that depends on
# this will always be rebuilt. Ref: https://stackoverflow.com/a/32062884/5351807
add_custom_command(
    OUTPUT always_rebuild
    COMMAND cmake -E echo
)

add_custom_command(
    OUTPUT
    objects.inv
    DEPENDS
    always_rebuild
    COMMAND
    ${PYTHON_EXECUTABLE} -m sphinx -b html ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR}
    COMMENT
    "Generating documentation with Sphinx"
    VERBATIM
)

add_custom_target(
    docs
    DEPENDS objects.inv
)
