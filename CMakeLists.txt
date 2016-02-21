cmake_minimum_required( VERSION 3.0.0 )
cmake_policy( SET CMP0048 NEW )
project( libapache2-mod-user-repo VERSION 1.2 LANGUAGES NONE )

set( SYSCONFDIR /etc CACHE PATH "System configuration directory" )
file( TO_NATIVE_PATH "${SYSCONFDIR}" SYSCONFDIR )

add_custom_target( man1 ALL DEPENDS README.md
  COMMAND mkdir -p man1
  COMMAND pandoc -s -t man README.md -o - | gzip > man1/user-repo.1.gz
  COMMAND ln -fs user-repo.1.gz man1/svnuser-new-repo.1.gz
  COMMAND ln -fs user-repo.1.gz man1/gituser-new-repo.1.gz
  COMMAND ln -fs user-repo.1.gz man1/svnuser-import-repo.1.gz
  COMMAND ln -fs user-repo.1.gz man1/gituser-import-repo.1.gz
  COMMAND ln -fs user-repo.1.gz man1/svnuser-init-user.1.gz
  COMMAND ln -fs user-repo.1.gz man1/gituser-init-user.1.gz )

add_custom_target( deb DEPENDS man1
  COMMAND mkdir deb ${PROJECT_NAME}-${PROJECT_VERSION}
  COMMAND rsync -av CMakeLists.txt debian README.md man1 svnuser-* gituser-* user-repo* ${PROJECT_NAME}-${PROJECT_VERSION}
  COMMAND tar -czf deb/${PROJECT_NAME}_${PROJECT_VERSION}.orig.tar.gz ${PROJECT_NAME}-${PROJECT_VERSION}
  COMMAND mv ${PROJECT_NAME}-${PROJECT_VERSION} deb
  COMMAND cd deb/${PROJECT_NAME}-${PROJECT_VERSION} && debuild -us -uc )

install( PROGRAMS svnuser-new-repo svnuser-import-repo gituser-new-repo gituser-import-repo DESTINATION bin )
install( PROGRAMS svnuser-init-user gituser-init-user DESTINATION sbin )
install( DIRECTORY man1 DESTINATION share/man )
install( FILES user-repo.load user-repo.conf user-repo-svn-users user-repo-git-users DESTINATION ${SYSCONFDIR}/apache2/mods-available )

add_custom_target( realclean cd ${CMAKE_HOME_DIRECTORY}
  COMMAND rm -fr CMakeFiles CMakeCache.txt cmake_install.cmake install_manifest.txt Makefile man1 deb ${PROJECT_NAME}-${PROJECT_VERSION} )
