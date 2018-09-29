fastlane_version "2.105.2"
default_platform(:ios)
PODSPEC_NAME = ENV['podspepc_name']

def check_current_work_status(env = :public, options)
  target_version = options[:version]
  if target_version.nil?
    UI.user_error!("target_version miss")
  end
  ensure_git_status_clean
  ensure_git_branch(branch: 'master')
end

# def perform_scan
#   scan(
#     workspace: "Example/#{PODSPEC_NAME}.xcworkspace",
#     scheme: "#{PODSPEC_NAME}Demo",
#     devices: ["iPhone 6s", "iPhone 7"],
#     output_directory: "~/Downloads"
#   )
# end

def perform_lint_lib
  pod_lib_lint(
    verbose: true, 
    allow_warnings: true,
    # sources: SOURCES,
    use_bundle_exec: true,
    fail_fast: false, 
    # use_libraries: true
  )
end

def update_version(options)
  target_version = options[:version]
  increment_version_number(version_number: target_version)
  version_bump_podspec(
    path: "#{PODSPEC_NAME}.podspec",
    version_number: target_version
  )
end

def commit_push_pod_push_tag(env, options)
  target_version = options[:version]

  # git commit -A
  begin
    git_commit_all(message: "Bump version to #{target_version}")
  rescue # rescue, because this raises an exception if it can't be found at all
  end

  # git push origin master
  push_to_git_remote

  # remove tag
  if git_tag_exists(tag: target_version)
    UI.message("发现 tag: #{target_version} 存在，即将执行删除动作 🚀")

    git_remove_tag(
      tag:target_version,
      isRL:true,
      isRR:true
    )
  end

  # add tag、push tag
  add_git_tag(tag: target_version)
  push_git_tags()
end

def podspec_push_to_repo(env, options)
  pod_push(
    path: "#{PODSPEC_NAME}.podspec",
    # repo: repo,
    # sources: SOURCES,
    allow_warnings: true,
    use_libraries: true
  )
end

def deploy_pod(env, options)
  update_version(options)
  commit_push_pod_push_tag(env, options)
  podspec_push_to_repo(env, options)
end

def perform_lane(env = :release, options)
  target_version = options[:version]
  # target_version = version_get_podspec(path: "#{PODSPEC_NAME}.podspec") if target_version.nil?
  UI.confirm("confirm push repo version is #{target_version}, pod is #{PODSPEC_NAME}")
  check_current_work_status(env, options)
  perform_lint_lib
  # perform_scan = options[:perform_scan]
  # if perform_scan
  #   perform_scan
  # end
  deploy_pod(env, options)
end

# deoloy cocoapods public xxx.podspec
########################################################################
# podspepc_name=XZHRuntime fastlane deploy_public_pod version:1.1.0
########################################################################
platform :deploy_public_pod do
  lane :release do |options|
    UI.message('begin deploy public pod')
    perform_lane(:release, options)
  end
end

# deoloy private xxx.podspec
########################################################################
# podspepc_name=XZHRuntime fastlane deploy_private_pod version:1.1.0
########################################################################
platform :deploy_private_pod do
  lane :release do |options|
    UI.message('begin deploy private pod')
    # perform_lane(:release, options)
  end
end