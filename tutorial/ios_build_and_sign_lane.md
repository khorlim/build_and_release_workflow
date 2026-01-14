lane :build_and_sign do
setup_ci
match()

    build_app(
      export_method: "app-store"
    )

end
